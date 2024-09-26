Notepad 
''',py

    import hashlib
    import pickle
    import os
    import secrets
    import string
    from playsound import playsound # type: ignore
    from cryptography.hazmat.primitives.asymmetric import rsa, padding
    from cryptography.hazmat.primitives import serialization, hashes
    from cryptography.hazmat.backends import default_backend
    from getpass import getpass  # Hides password input for security
    import os  # Used to check if files exist
    from colorama import Fore, Style, init  # For colored terminal output #type:ignore

    from datetime import datetime  # To store note timestamps

    # Initialize colorama for Windows compatibility
    init(autoreset=True)

    # File to store passwords
    PASSWORD_FILE = "passwords.pkl"
    PRIVATE_KEY_FILE = "private_key.pem"
    PUBLIC_KEY_FILE = "public_key.pem"

    # File to save notes
    NOTEPAD = "notes.pkl"

    # Data containers
    notepadsave = []  # List to store notes
    Password = []
    Site = []

    MASTER_PASSWORD = "isak2026"  # The master password for decrypting

    # Main function
    def main():
    load_passwords()  # Load passwords from file at the start
    load_notes()  # Load notes from file at the start

    print(Fore.CYAN + "\nWelcome to the Basic Notepad !")
    validation = input("Type 'yes' to start taking notes: ")

    if validation.lower() == 'yes' or 'y':
        notepad_menu()
    else:
        if input(Fore.YELLOW + "\nDo you want to exit the app? (yes/no): ").lower() == "no" or "n":
            main()
        else:
            exit()

    # Notepad Menu
    def notepad_menu():
    while True:
        print(Fore.CYAN + "\nNotepad Menu:")
        print(Fore.GREEN + "1. Add New Note")
        print(Fore.GREEN + "2. Retrieve Notes by Date")
        print(Fore.GREEN + "3. View All Notes")
        print(Fore.GREEN + "4. Exit to Main Menu")

        choice = input(Fore.YELLOW + "\nSelect an option (1-4): ")

        if choice == "1":
            add_note()
        elif choice == "2":
            retrieve_notes_by_date()
        elif choice == "3":
            view_all_notes()
        elif choice == "4":
            main()
        else:
            print(Fore.RED + "Invalid option, please choose a valid number.")

    # Add a new note with timestamp
    def add_note():
    print(Fore.CYAN + "\nAdd a new note:")
    note_content = input(Fore.GREEN + "Please enter your note: ")

    if note_content == MASTER_PASSWORD:
        Passwordsaver()
    if note_content:
        timestamp = datetime.now().strftime('%Y-%m-%d %H:%M:%S')  # Save the current date and time
        note_entry = {"note": note_content, "timestamp": timestamp}
        notepadsave.append(note_entry)

        # Save the note to file
        save_notes()
        print(Fore.GREEN + f"\nYour note has been saved with the timestamp: {timestamp}")

        if input(Fore.YELLOW + "\nDo you want to add another note? (yes/no): ").lower() == "yes":
            add_note()
    else:
        print(Fore.RED + "Note cannot be empty.")

    # Retrieve notes by date
    def retrieve_notes_by_date():
    print(Fore.CYAN + "\nRetrieve Notes by Date:")

    date_input = input(Fore.YELLOW + "Please enter the date (YYYY-MM-DD): ")

    try:
        datetime.strptime(date_input, '%Y-%m-%d')  # Check if the date is valid

        matching_notes = [note for note in notepadsave if note['timestamp'].startswith(date_input)]
    
        if matching_notes:
            print(Fore.GREEN + f"\nNotes saved on {date_input}:")
            for note in matching_notes:
                print(Fore.CYAN + f"Timestamp: {note['timestamp']}")
                print(Fore.GREEN + f"Note: {note['note']}\n")
               
        else:
            print(Fore.RED + f"\nNo notes found for {date_input}.")
    except ValueError:
        print(Fore.RED + "Invalid date format. Please enter the date as YYYY-MM-DD.")

    # View all notes
    def view_all_notes():
    if not notepadsave:
        print(Fore.RED + "No notes saved.")
    else:
        print(Fore.CYAN + "\nAll Saved Notes:")
        for note in notepadsave:
            print(Fore.GREEN + f"Timestamp: {note['timestamp']}")
            print(Fore.GREEN + f"Note: {note['note']}\n")

    # RSA key generation
    def generate_rsa_keys():
    print(Fore.CYAN + "Generating RSA keys...")
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

    # Password saver with RSA encryption
    def Passwordsaver():
    # Clear the terminal based on the OS
    if os.name == 'nt':  # For Windows
        os.system('cls')
    else:  # For MacOS/Linux
        os.system('clear')


    private_key, public_key = load_rsa_keys()

    while True:
        print(Fore.CYAN + "\nChoose one of the following options:")
        print(Fore.GREEN + "1. Save Password")
        print(Fore.GREEN + "2. View Password")
        print(Fore.GREEN + "3. Generate Random Password and Save")
        print(Fore.GREEN + "4. Delete Password")
        print(Fore.GREEN + "5. Update Password")
        print(Fore.GREEN + "6. Exit")
        Continue = input(Fore.YELLOW + "\nSelect an option (1-6): ")

        if Continue == "1":
            save_password_flow(public_key)
        elif Continue == "2":
            view_password_flow(private_key)
        elif Continue == "3":
            generate_and_save_password(public_key)
        elif Continue == "4":
            delete_password_flow()
        elif Continue == "5":
            update_password_flow(private_key, public_key)
        elif Continue == "6":
            main()
        else:
            print(Fore.RED + "Invalid option, please try again.")

    # Save passwords
    def save_password_flow(public_key):
    while True:
        site = input(Fore.CYAN + "Input the site: ")
        password = getpass(Fore.CYAN + "Input the password for the site: ")  # Using getpass to hide password input

        encrypted_password = rsa_encrypt_password(public_key, password)
        Site.append(site)
        Password.append(encrypted_password)

        save_passwords()

        if input(Fore.YELLOW + "\nDo you want to save another password? (yes/no): ").lower() != "yes":
            break

    # View passwords
    def view_password_flow(private_key):
    if not Password:
        print(Fore.RED + "No passwords saved.")
    else:
        print(Fore.GREEN + "\nStored sites:")
        for i, site in enumerate(Site):
            print(Fore.CYAN + f"{i + 1}. {site}")
    
        try:
            choice = int(input(Fore.YELLOW + "\nEnter the number of the site you want to view the password for: ")) - 1
            if 0 <= choice < len(Site):
                view_choice = input(Fore.YELLOW + f"\nDo you want to view the encrypted password for '{Site[choice]}'? (yes/no): ").lower()
            
                if view_choice == "yes":
                    print(Fore.CYAN + f"\nEncrypted Password for '{Site[choice]}': {Password[choice]}")
                else:
                    decrypt_choice = input(Fore.YELLOW + f"\nDo you want to decrypt the password for '{Site[choice]}'? (yes/no): ").lower()
                    if decrypt_choice == "yes":
                        entered_master_password = getpass(Fore.YELLOW + "\nPlease enter the master password: ")
                        if entered_master_password == MASTER_PASSWORD:
                            decrypted_password = rsa_decrypt_password(private_key, Password[choice])
                            if decrypted_password is not None:
                                    print(Fore.GREEN + f"\nDecrypted Password for '{Site[choice]}': {decrypted_password}")
                            else:
                                print(Fore.RED + "Failed to decrypt the password.")
                        else:
                            print(Fore.RED + "Incorrect master password.")
                    else:
                        print(Fore.CYAN + "Decryption cancelled.")
            else:
                print(Fore.RED + "Invalid site number.")
        except ValueError:
            print(Fore.RED + "Invalid input. Please enter a valid number.")
    
    
    # Function to generate a random password
    def generate_random_password(length):
    if length < 1:
        print(Fore.RED + "Password length must be greater than 0.")
        return None
    
    # Define possible characters
    alphabet = string.ascii_letters + string.digits + string.punctuation
    password = ''.join(secrets.choice(alphabet) for _ in range(length))
    
    return password
    
    # Function to generate and save a random password
    def generate_and_save_password(public_key):
    site = input(Fore.CYAN + "Input the site for which to generate a password: ")
    
    try:
        length = int(input(Fore.CYAN + "Input the desired password length: "))
        password = generate_random_password(length)
    
        if password:
            print(Fore.GREEN + f"Generated Password: {password}")
            
            # Encrypt the generated password
            encrypted_password = rsa_encrypt_password(public_key, password)
    
            # Save site and encrypted password
            Site.append(site)
            Password.append(encrypted_password)
    
            save_passwords()
    
            print(Fore.GREEN + "Generated password has been saved successfully.")
        else:
            print(Fore.RED + "Password generation failed.")
    except ValueError:
        print(Fore.RED + "Invalid input for password length. Please enter a valid number.")
    
    
    
    # Delete password
    def delete_password_flow():
    if not Password:
        print(Fore.RED + "No passwords saved.")
    else:
        print(Fore.GREEN + "\nStored sites:")
        for i, site in enumerate(Site):
            print(Fore.CYAN + f"{i + 1}. {site}")
        
        try:
            choice = int(input(Fore.YELLOW + "\nEnter the number of the site you want to delete the password for: ")) - 1
            if 0 <= choice < len(Site):
                entered_master_password = getpass(Fore.YELLOW + "\nPlease enter the master password: ")
                if entered_master_password == MASTER_PASSWORD:
                    print(Fore.GREEN + f"\nDeleting password for '{Site[choice]}'...")
                    del Site[choice]
                    del Password[choice]
                    save_passwords()
                    print(Fore.GREEN + "Password deleted successfully.")
                else:
                    print(Fore.RED + "Incorrect master password.")
            else:
                print(Fore.RED + "Invalid site number.")
        except ValueError:
            print(Fore.RED + "Invalid input. Please enter a valid number.")
    
    # Update password
    def update_password_flow(private_key, public_key):
    if not Password:
        print(Fore.RED + "No passwords saved.")
    else:
        print(Fore.GREEN + "\nStored sites:")
        for i, site in enumerate(Site):
            print(Fore.CYAN + f"{i + 1}. {site}")
    
        try:
            choice = int(input(Fore.YELLOW + "\nEnter the number of the site you want to update the password for: ")) - 1
            if 0 <= choice < len(Site):
                entered_master_password = getpass(Fore.YELLOW + "\nPlease enter the master password: ")
                if entered_master_password == MASTER_PASSWORD:
                    decrypted_password = rsa_decrypt_password(private_key, Password[choice])
                    if decrypted_password is not None:
                        previous_password = getpass(Fore.CYAN + f"\nEnter the previous password for '{Site[choice]}': ")
                        if decrypted_password == previous_password:
                            new_password = getpass(Fore.CYAN + "\nEnter the new password: ")
                            encrypted_password = rsa_encrypt_password(public_key, new_password)
                            Password[choice] = encrypted_password
                            save_passwords()
                            print(Fore.GREEN + f"\nPassword for '{Site[choice]}' updated successfully.")
                        else:
                            print(Fore.RED + "Previous password is incorrect.")
                    else:
                        print(Fore.RED + "Failed to decrypt the previous password.")
                else:
                    print(Fore.RED + "Incorrect master password.")
            else:
                print(Fore.RED + "Invalid site number.")
        except ValueError:
            print(Fore.RED + "Invalid input. Please enter a valid number.")
    
    # Encrypt password
    def rsa_encrypt_password(public_key, password):
    return public_key.encrypt(
        password.encode(),
        padding.OAEP(
            mgf=padding.MGF1(algorithm=hashes.SHA256()),
            algorithm=hashes.SHA256(),
            label=None
        )
    )
    
    # Decrypt password
    def rsa_decrypt_password(private_key, encrypted_password):
    try:
        return private_key.decrypt(
            encrypted_password,
            padding.OAEP(
                mgf=padding.MGF1(algorithm=hashes.SHA256()),
                algorithm=hashes.SHA256(),
                label=None
            )
        ).decode()
    except Exception as e:
        print(Fore.RED + f"Decryption error: {e}")
        return None
    
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
    
    # Save notes to file
    def save_notes():
    with open(NOTEPAD, "wb") as f:
        pickle.dump(notepadsave, f)
    
    # Load notes from file
    def load_notes():
    global notepadsave
    if os.path.exists(NOTEPAD):
        with open(NOTEPAD, "rb") as f:
            notepadsave = pickle.load(f)
    else:
        notepadsave = []
    
    # Function to play a sound notification
    def play_notification_sound():
    try:
        
        playsound('ding.mp3')
    except Exception as e:
        print(Fore.RED + f"Error playing sound: {e}")
    
    
    if __name__ == "__main__":
    if not (os.path.exists(PRIVATE_KEY_FILE) and os.path.exists(PUBLIC_KEY_FILE)):
        generate_rsa_keys()
    
        main()
    
