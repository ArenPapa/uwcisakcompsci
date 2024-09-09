Given a string, create a program that produces the sum of the characters in the string.


''',py

    def calculate_char_sum(input_string):
        """(NOTE)Dr . Ruben i use the ASCII to Calculate the sum of  values of characters in the input string."""
        total_sum = 0
        for char in input_string:
            total_sum += ord(char)
        return total_sum

    def main():
  
        input_string = input("Enter a string: ")
    
  
        char_sum = calculate_char_sum(input_string)
    
  
        print(f"Sum of ASCII values of characters: {char_sum}")

    if __name__ == "__main__":
        main()
