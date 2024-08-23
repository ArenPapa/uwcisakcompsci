# Error Correction code 


''' 
   'py

     def ErrorCorrection():

      bit = input("enter your binary")
      num_characters = len(bit)
      x = 0 
      security = 0 

   
      while x <= num_characters:
     
        if bit[x] == "1":
            security = security + 1
        else:
            security = security

        x = x +1 

        if x == num_characters:
            break
    
      if security%2 == 0:
        bit = "1" + bit 
      elif security == 0:
        bit = "0" + bit 
      else:
        bit = "0" + bit
    
      if bit[0] == "1":
        print("true" + bit)
      elif bit[0] == "0":
        print("false"+ bit)
      else: 
        error = input("something went wrong, do you wanna try again?:")
        if error == "yes":
            ErrorCorrection()
        else:
            exit()


    while True:  
    ask = input("do you want to store information:")

    if ask == "yes":
        ErrorCorrection()
    else:
        exit()
'''
