project 1 make a simple calculator for a company 
''',py

    import hashlib
    import pickle  
    from cryptography.hazmat.primitives.asymmetric import rsa, padding
    from cryptography.hazmat.primitives import serialization, hashes
    from cryptography.hazmat.backends import default_backend
    from getpass import getpass  # Hides password input for security
    import os  # Used to check if files exist

    # File to store passwords
    PASSWORD_FILE = "passwords.pkl"
    PRIVATE_KEY_FILE = "private_key.pem"
    PUBLIC_KEY_FILE = "public_key.pem"

    # Data containers
    notepadsave = []
    Password = []
    Site = []

    MASTER_PASSWORD = "isak2026"  # The master password for decrypting

    # Main function
    def main():
            load_passwords()  # Load passwords from file at the start
    
        validation = input("Welcome to the basic notepad app! Type 'yes' to start taking notes: ")

        if validation.lower() == 'yes':
            notepad()
        else:
            if input("Do you want to exit the app? (yes/no): ").lower() == "no":
                main()
            elif input("Enter master password to access the Password Saver: ") == MASTER_PASSWORD:
                Passwordsaver()
            else:
                exit()

    # Basic notepad
    def notepad():
        notes = input("Please start entering your notes: ")
        if notes == MASTER_PASSWORD:
            Passwordsaver()
        else:
            save_notes = input("Do you want to save your notes? (yes/no): ").lower()
            if save_notes == "yes":
                notepadsave.append(notes)
                if input("Do you want to continue taking notes? (yes/no): ").lower() == "yes":
                    notepad()
                else:
                    exit()

    # RSA key generation
    def generate_rsa_keys():
        print("Generating RSA keys...")
        private_key = rsa.generate_private_key(
            public_exponent=65537,
            key_size=2048,
            backend=default_backend()
    )
    
        public_key = private_key.public_key()

        # Save private key
        with open(PRIVATE_KEY_FILE, "wb") as f:
            f.write(private_key.private_bytes(
                encoding=serialization.Encoding.PEM,
                format=serialization.PrivateFormat.TraditionalOpenSSL,
                encryption_algorithm=serialization.NoEncryption()
            ))

        # Save public key
        with open(PUBLIC_KEY_FILE, "wb") as f:
            f.write(public_key.public_bytes(
                encoding=serialization.Encoding.PEM,
                format=serialization.PublicFormat.SubjectPublicKeyInfo
            ))

    # Load RSA keys from file
    def load_rsa_keys():
        with open(PRIVATE_KEY_FILE, "rb") as f:
            private_key = serialization.load_pem_private_key(
                f.read(),
                password=None,
                backend=default_backend()
            )

        with open(PUBLIC_KEY_FILE, "rb") as f:
            public_key = serialization.load_pem_public_key(
                f.read(),
                backend=default_backend()
            )

        return private_key, public_key

    # Encrypt password using RSA public key
    def rsa_encrypt_password(public_key, password):
        encrypted_password = public_key.encrypt(
            password.encode(),
            padding.OAEP(
                mgf=padding.MGF1(algorithm=hashes.SHA256()),
                algorithm=hashes.SHA256(),
                label=None
            )
        )
        return encrypted_password

    # Decrypt password using RSA private key
    def rsa_decrypt_password(private_key, encrypted_password):
        # Check if the password is in bytes, as encrypted passwords should be in bytes
        if isinstance(encrypted_password, bytes):
            try:
                decrypted_password = private_key.decrypt(
                    encrypted_password,
                    padding.OAEP(
                        mgf=padding.MGF1(algorithm=hashes.SHA256()),
                        algorithm=hashes.SHA256(),
                        label=None
                    )
                )
                return decrypted_password.decode()
            except ValueError:
                print("Decryption failed. The encrypted password might be corrupted or incomplete.")
                return None
        else:
            # If it's not in bytes, assume it's already plaintext (for older stored passwords)
            print("Password is not encrypted, returning as plaintext.")
            return encrypted_password

    # Password saver with RSA encryption
    def Passwordsaver():
        private_key, public_key = load_rsa_keys()

        while True:
            Continue = input("Welcome to the password saver. Choose one of the following options: View / Save / Delete / Exit: ").lower()

            if Continue == "save":
                save_password_flow(public_key)
            elif Continue == "view":
                view_password_flow(private_key)
            elif Continue == "delete":
                delete_password_flow()
            elif Continue == "exit":
                save_passwords()
                main()
            else:
                print("Invalid option, please try again.")

    # Flow for saving passwords
    def save_password_flow(public_key):
        while True:
            site = input("Input the site: ")
            password = getpass("Input the password for the site: ")  # Using getpass to hide password input

            # Encrypt the password using RSA
            encrypted_password = rsa_encrypt_password(public_key, password)
            Site.append(site)
            Password.append(encrypted_password)

            save_passwords()

            if input("Do you want to save another password? (yes/no): ").lower() != "yes":
                break

    # Flow for viewing passwords
    def view_password_flow(private_key):
        if not Password:
            print("No passwords saved.")
        else:
            # Display all sites and encrypted passwords
            print("\nStored sites and their encrypted passwords:")
            for i in range(len(Site)):
                print(f"{i + 1}. Site: {Site[i]}, Encrypted Password: {Password[i]}")
        
            try:
                # Ask the user which site they want to decrypt the password for
                choice = int(input("\nEnter the number of the site you want to decrypt the password for: ")) - 1
            
                if 0 <= choice < len(Site):
                    decrypt_choice = input(f"Do you want to decrypt the password for '{Site[choice]}'? (yes/no): ").lower()
                
                    if decrypt_choice == "yes":
                        entered_master_password = getpass("Please enter the password: ")
                    
                        if entered_master_password == MASTER_PASSWORD:
                            decrypted_password = rsa_decrypt_password(private_key, Password[choice])
                            if decrypted_password is not None:
                                print(f"Decrypted Password for '{Site[choice]}': {decrypted_password}")
                            else:
                                print("Failed to decrypt the password.")
                        else:
                            print("Incorrect password.")
                    else:
                        print("Decryption cancelled.")
                else:
                    print("Invalid site number. Please try again.")
            except ValueError:
                print("Invalid input. Please enter a valid number.")

    # Flow for deleting passwords
    def delete_password_flow():
        site_to_delete = input("Enter the site for which you want to delete the password: ")
        if site_to_delete in Site:
            index = Site.index(site_to_delete)
            confirmation = input(f"Are you sure you want to delete the password for '{site_to_delete}'? (yes/no): ").lower()
            if confirmation == "yes":
                del Site[index]
                del Password[index]
                save_passwords()
                print(f"Password for site '{site_to_delete}' has been deleted.")
        else:
            print("Site not found.")

    # Save passwords to file
    def save_passwords():
        with open(PASSWORD_FILE, "wb") as f:
            pickle.dump({"sites": Site, "passwords": Password}, f)

    # Load passwords from file
    def load_passwords():
        global Site, Password
        if os.path.exists(PASSWORD_FILE):
            with open(PASSWORD_FILE, "rb") as f:
                data = pickle.load(f)
                Site = data.get("sites", [])
                Password = data.get("passwords", [])
        else:
            Site = []
            Password = []

    if __name__ == "__main__":
        # Check if RSA keys exist; if not, generate new ones
        if not (os.path.exists(PRIVATE_KEY_FILE) and os.path.exists(PUBLIC_KEY_FILE)):
            generate_rsa_keys()

        main()

