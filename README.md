import colorama
from termcolor import colored
import datetime
import math

options_message = """
Wybierz opcję:
1. Wybierz parking
2. Dodaj nowy parking
3. Parkuj pojazd
4. Usuń pojazd z parkingu
5. Pokaż układ parkingu
6. Generuj bilet parkingowy
7. Sprawdź status wjazdu
8. Przypisz abonament
9. Wyświetl statystyki abonamentowe
10. Wyjście
"""

parkings = []

import math

def calculate_parking_cost(parking_duration):
    pierwsza_godzina = 2,50
    kazda_nastepna = 2

    total_hours = math.ceil(parking_duration.total_seconds() / 3600)
    if total_hours <= 1:
        return pierwsza_godzina
    else:
        return pierwsza_godzina + (suma_godzin - 1) * additional_hour_cost
class Vehicle:
    def __init__(self, v_type, v_number):
        self.v_type = v_type
        self.v_number = v_number
        self.entry_time = None
        self.is_subscription = False

    def __str__(self):
        return self.v_number


class Slot:
    def __init__(self):
        self.vehicle = None

    @property
    def is_empty(self):
        return self.vehicle is None


class Parking:
    def __init__(self, name, rows, columns):
        self.name = name
        self.rows = rows
        self.columns = columns
        self.slots = self._get_slots(rows, columns)

    def start(self):
        while True:
            try:
                print(options_message)

                option = input("Wybierz opcję: ")

                if option == '1':
                    self._choose_parking()

                if option == '2':
                    self._add_parking()

                if option == '3':
                    self._park_vehicle()

                if option == '4':
                    self._remove_vehicle()

                if option == '5':
                    self.show_layout()

                if option == '6':
                    self._generate_ticket()

                if option == '7':
                    self._check_entry_exit_status()

                if option == '8':
                    self._assign_subscription()

                if option == '9':
                    self._display_subscription_statistics()

                if option == '10':
                    break

            except ValueError as e:
                print(colored(f"Wystąpił błąd: {e}. Spróbuj ponownie.", "red"))

        print(colored("Dziękujemy za skorzystanie z naszego systemu pomocy parkingowej.", "green"))

    def _choose_parking(self):
        print("Dostępne parkingi:")
        for i, parking in enumerate(parkings, 1):
            print(f"{i}. {parking.name}")

        choice = self._get_safe_int("Wybierz numer parkingu: ")
        if choice <= 0 or choice > len(parkings):
            raise ValueError("Nieprawidłowy numer parkingu.")

        chosen_parking = parkings[choice - 1]
        print(colored(f"Wybrano parking: {chosen_parking.name}", "green"))
        chosen_parking.start()

    def _add_parking(self):
        name = input("Podaj nazwę nowego parkingu: ")
        rows = self._get_safe_int("Podaj liczbę wierszy: ")
        columns = self._get_safe_int("Podaj liczbę kolumn: ")
        new_parking = Parking(name, rows, columns)
        parkings.append(new_parking)
        print(colored(f"Dodano parking: {new_parking.name}", "green"))

    def _park_vehicle(self):
        v_type = input("Podaj rodzaj pojazdu: ")
        v_number = input("Podaj numer rejestracyjny pojazdu: ")

        row = self._get_safe_int("Podaj numer wiersza: ")
        column = self._get_safe_int("Podaj numer kolumny: ")

        if row <= 0 or row > self.rows or column <= 0 or column > self.columns:
            raise ValueError("Nieprawidłowe współrzędne miejsca parkingowego.")

        slot = self.slots[row - 1][column - 1]

        if slot.is_empty:
            vehicle = Vehicle(v_type, v_number)
            vehicle.entry_time = datetime.datetime.now()
            slot.vehicle = vehicle
            print(colored(f"Pojazd {vehicle.v_number} został zaparkowany.", "green"))
        else:
            print(colored("Wybrane miejsce parkingowe jest zajęte.", "yellow"))

    def _remove_vehicle(self):
        v_number = input("Podaj numer rejestracyjny pojazdu: ")

        for row in self.slots:
            for slot in row:
                if not slot.is_empty and slot.vehicle.v_number == v_number:
                    vehicle = slot.vehicle
                    slot.vehicle = None
                    print(colored(f"Pojazd {vehicle.v_number} został usunięty z parkingu.", "green"))
                    return

        print(colored(f"Pojazd o numerze rejestracyjnym {v_number} nie został znaleziony na parkingu.", "yellow"))

    def show_layout(self):
        for row in self.slots:
            for slot in row:
                if slot.is_empty:
                    print(colored("X", "red"), end=" ")
                else:
                    print(colored("O", "green"), end=" ")
            print()

    def _generate_ticket(self):
        v_number = input("Podaj numer rejestracyjny pojazdu: ")

        for row in self.slots:
            for slot in row:
                if not slot.is_empty and slot.vehicle.v_number == v_number:
                    vehicle = slot.vehicle
                    if vehicle.entry_time is not None:
                        current_time = datetime.datetime.now()
                        elapsed_time = current_time - vehicle.entry_time
                        print(colored(f"Bilet parkingowy dla pojazdu {vehicle.v_number}:"))
                        print(colored(f"Data wejścia: {vehicle.entry_time}"))
                        print(colored(f"Czas postoju: {elapsed_time}"))
                        return

        print(colored(f"Pojazd o numerze rejestracyjnym {v_number} nie został znaleziony na parkingu.", "yellow"))

    def _check_entry_exit_status(self):
        v_number = input("Podaj numer rejestracyjny pojazdu: ")

        for row in self.slots:
            for slot in row:
                if not slot.is_empty and slot.vehicle.v_number == v_number:
                    vehicle = slot.vehicle
                    if vehicle.entry_time is not None:
                        current_time = datetime.datetime.now()
                        parking_duration = current_time - vehicle.entry_time
                        parking_cost = calculate_parking_cost(parking_duration)  # funkcja do obliczania kosztu postoju

                        print(f"Pojazd {vehicle.v_number} wjechał na parking o godzinie {vehicle.entry_time}.")
                        print(f"Czas postoju: {parking_duration}")
                        print(f"Koszt postoju: {parking_cost}")
                    else:
                        print(f"Pojazd {vehicle.v_number} nie wjechał na parking.")
                    return

        print(f"Pojazd o numerze rejestracyjnym {v_number} nie został znaleziony na parkingu.")

    def _assign_subscription(self):
        v_number = input("Podaj numer rejestracyjny pojazdu: ")

        for row in self.slots:
            for slot in row:
                if not slot.is_empty and slot.vehicle.v_number == v_number:
                    vehicle = slot.vehicle
                    vehicle.is_subscription = True
                    print(colored(f"Pojazd {vehicle.v_number} otrzymał abonament.", "green"))
                    return

        print(colored(f"Pojazd o numerze rejestracyjnym {v_number} nie został znaleziony na parkingu.", "yellow"))

    def _display_subscription_statistics(self):
        count = 0
        for row in self.slots:
            for slot in row:
                if not slot.is_empty and slot.vehicle.is_subscription:
                    count += 1

        print(f"Liczba pojazdów z abonamentem na parkingu {self.name}: {count}")

    @staticmethod
    def _get_safe_int(prompt):
        while True:
            try:
                value = int(input(prompt))
                return value
            except ValueError:
                print(colored("Wprowadź liczbę całkowitą.", "red"))

    @staticmethod
    def _get_slots(rows, columns):
        slots = []
        for _ in range(rows):
            row = [Slot() for _ in range(columns)]
            slots.append(row)
        return slots


def main():
    colorama.init()
    parking = Parking("Parking", 5, 5)
    parkings.append(parking)
    parking.start()


if __name__ == '__main__':
    main()
