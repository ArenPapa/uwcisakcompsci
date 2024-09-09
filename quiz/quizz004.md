Given a number, create a program that produces the output factors.


''',py

    def find_factors(number):
    
        factors = []
        for i in range(1, number):
            if number % i == 0:
                factors.append(i)
        return factors

    def is_perfect_number(number):
    
        factors = find_factors(number)
        return sum(factors) == number

    def main():
    
        try:
            number = int(input("Enter a number: "))
        
            if number <= 0:
                print("Please enter a positive integer.")
                return
        
        
            factors = find_factors(number)
            print(f"Factors of {number}: {factors}")
        
    
            if is_perfect_number(number):
                print(f"{number} is a Perfect Number: True")
            else:
                print(f"{number} is a Perfect Number: False")
    
        except ValueError:
            print("Invalid input. Please enter a valid integer.")

    if __name__ == "__main__":
        main()
