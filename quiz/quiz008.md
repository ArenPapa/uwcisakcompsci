hotel app

''',py

    def print_rooms(num_floors, rooms_per_floor):
        hotel = {}
        for floor in range(1, num_floors + 1):
            for room in range(1, rooms_per_floor + 1):
                hotel[f"Room {floor}{room:02d}"] = floor
        for room, floor in hotel.items():
            print(f"{room} is on floor {floor}")
        return hotel


    def search_room(hotel):
        room_to_search = input("Enter the room number you want to search for (e.g., 201): ").strip()
        room_key = f"Room {room_to_search}"
        if room_key in hotel:
            print(f"{room_key} is on floor {hotel[room_key]}")
        else:
            print(f"{room_key} does not exist.")


    def main():
        num_floors = int(input("Enter the number of floors in the hotel: "))
        rooms_per_floor = int(input("Enter the number of rooms on each floor: "))
    

        hotel = print_rooms(num_floors, rooms_per_floor)
    

        search = input("Do you want to search for a particular room? (yes/no): ").lower().strip()
        if search == 'yes':
            search_room(hotel)
        else:
            print("Thank you for using the hotel system!")

    if __name__ == "__main__":
        main()
