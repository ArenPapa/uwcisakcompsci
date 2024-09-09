project 1 make a simple calculator for a company 
''',py

    notepadsave = []
    Password = []
    Site = []
    RotPass = []

    MASTER_PASSWORD = "isak2026"  # The master password for decrypting

    # Main function
    def main():
        validation = input("Welcome to the basic notepad app! Type 'yes' to start taking notes: ")

        if validation in ['YES', 'Yes', 'yes']:
            notepad()
        else:
            validation = input("Do you want to exit the app? (yes/no): ")
            if validation == "no":
                main()
            elif validation == MASTER_PASSWORD:
                Passwordsaver()
            else:
                exit()

    # Basic notepad
    def notepad():
        notes = input("Please start entering your notes: ")
        if notes == MASTER_PASSWORD:
            Passwordsaver()
        else:
            save = input("Do you want to save your notes? (yes/no): ")
            if save == "yes":
                notepadsave.append(notes)
                Continue = input("Do you want to continue taking notes? (yes/no): ")
                if Continue == "yes":
                    notepad()
                else:
                    exit()
            else:
                Continue = input("Do you want to continue taking notes? (yes/no): ")
                if Continue == "yes":
                    notepad()
                else:
                    exit()

    # Password saver with encryptiony
    def Passwordsaver():
        Continue = input("Welcome to the password saver. Choose one of the following options: View passwords / Save password / Decrypt passwords: ")

        if Continue == "Save password":
            while True:
                site = input("Input the site: ")
                password = input("Input the password of the site: ")

                encrypted_password = Encryption(password)  # Encrypt the password before saving
                Site.append(site)
                Password.append(encrypted_password)

                Continue = input("Do you want to save another password? (yes/no): ")
                if Continue != "yes":
                    Passwordsaver()

        elif Continue == "View passwords":
            if not Password:
                print("No passwords saved.")
                main()
            else:
                for i in range(len(Password)):
                    print(f"Site: {Site[i]}, Encrypted Password: {Password[i]}")

        elif Continue == "Decrypt passwords":
            entered_password = input("Enter the master password to view decrypted passwords: ")

            if entered_password == MASTER_PASSWORD:
                if not Password:
                    print("No passwords to decrypt.")
                else:
                    for i in range(len(Password)):
                        decrypted_password = Decryption(Password[i])  # Decrypt the password
                        print(f"Site: {Site[i]}, Decrypted Password: {decrypted_password}")
            else:
                print("Incorrect master password. Returning to main menu.")
                main()

        else:
            print("Invalid option. Returning to main menu.")
            main()

    #Encryption
    def Encryption(s):
        chars = "abcdefghijklmnopqrstuvwxyz"
        trans = chars[13:] + chars[:13]
        encrypted = ''
        for c in s:
            if c in chars:
                encrypted += trans[chars.index(c)]
            elif c.lower() in chars:  # For handling uppercase letters
                encrypted += trans[chars.index(c.lower())].upper()
            else:
                encrypted += c
        return encrypted


    def Decryption(s):
        return Encryption(s)  # ROT13 encryption and decryption are identical

    if __name__ == "__main__":
        main()
