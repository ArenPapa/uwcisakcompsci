# Email maker 

## The user enters his first name, last name, year of birth and prefered domain and the program is creating an email adress for him

''' 
  ,py 

    import random 

    domain_list = ['@gmail.com','@yahoo.com','@yahoo.co.uk','@outlook.com','@hotmail.com','@aol.com','@icloud.com','@mail.com']

    def email():
      RandomDomain = random.choice(domain_list)
      FirstName = input("Type your first name: ")
      LastName = input("Type your last name: ")
      BirthDay = input("Type the year you were born: ")
      options = input("What type of domain do you want? Option 1: Gmail account, Option 2: isak account, Option 3: random ")


      if options == "Gmail":
          print('Your email address is ' + FirstName + LastName + BirthDay + '@gmail.com')

      elif options == "isak":
        print('Your email address is ' + FirstName + LastName + BirthDay + '@uwcisak.jp')

      elif options == "random":
        print('Your email address is ' + FirstName + LastName + BirthDay + RandomDomain)

      else:
        print("Invalid option selected.")
        
    email()

    while True:
      Again = input("do you want to continue with another mail adress? (yes/no)")

      if Again == "yes":
          email()
      elif Again == "no":
          exit()
      else:
          exit()

'''
