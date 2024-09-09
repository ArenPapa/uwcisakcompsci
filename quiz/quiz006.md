Create a program that create 10 random passwords with digits and letters of length 20.

''',py

    import random
    import string

    def generate_password(length, include_symbols):
    
        characters = string.ascii_letters + string.digits
        if include_symbols:
            characters += string.punctuation  # Add symbols

        password = ''.join(random.choice(characters) for _ in range(length))
        return password

    def main():
    
        while True:
            try:
                length = int(input("Enter the length of the password: "))
                if length < 1:
                    print("Length must be at least 1.")
                    continue
                break
            except ValueError:
                print("Please enter a valid integer.")

    
        include_symbols_input = input("Include symbols (yes/no): ").strip().lower()
        include_symbols = include_symbols_input == 'yes'

   
        print("\033[91m")  
        for i in range(10):
            password = generate_password(length, include_symbols)
            print(f"Password {i + 1}: {password}")
        print("\033[0m")  

    if __name__ == "__main__":
        main()
