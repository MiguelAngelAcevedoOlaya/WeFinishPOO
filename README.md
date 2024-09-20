# Reto 7

In this implementation, we introduced an OrderIterator class to facilitate iteration over the items in an order. 

Iterator Class: The OrderIterator class encapsulates the logic for iterating over the menuitems list in the Order class. It keeps track of the current index and defines how to retrieve the next item.

__iter__() Method: We implemented the __iter__() method in the Order class to return an instance of OrderIterator. This makes the Order class iterable, allowing us to use it in a loop directly.

__next__() Method: Inside the OrderIterator, the __next__() method checks if there are more items to iterate over. If so, it returns the current item and increments the index. If there are no more items, it raises a StopIteration exception, signaling the end of the iteration.

Using the Iterator: When we loop through the Order object using a for loop, Python automatically calls the __iter__() method to get the iterator, and then repeatedly calls the __next__() method to access each item.

```python
class Menuitem:
    def __init__(self, name, price=0):
        self._name = name
        self._price = price

    def get_name(self):
        return self._name

    def get_price(self):
        return self._price

    def set_price(self, price):
        self._price = price

    def __str__(self):
        return f"{self.get_name()} - ${self.get_price():.2f}"


class Pay_method:
    def __init__(self, amount):
        self.amount = amount
    
    def pagar(self, cantidad):
        pass


class Card(Pay_method):
    def __init__(self, amount, tipe, number, cvc):
        super().__init__(amount)
        self.tipe = tipe
        self.number = number
        self.cvc = cvc
    
    def pagar(self):
        try:
            pago = f"Your payment of ${self.amount} with card number {self.number[-4:]} was successful."
            return pago
        except Exception as e:
            return f"An error occurred while processing the card payment: {e}"


class Cash(Pay_method):
    def __init__(self, cash, amount, correct=False, vueltas=0):
        super().__init__(amount)
        self.cash = cash
        self.correct = correct
        self.vueltas = vueltas
        
    def cambio(self):
        if self.cash < self.amount:
            print("Your money is not enough.")
            self.correct = False
        elif self.cash == self.amount:
            self.vueltas = 0
            self.correct = True
        elif self.cash > self.amount:
            self.vueltas = self.cash - self.amount
            self.correct = True
        return self.correct
        
    def pagar(self):
        if self.vueltas == 0:
            return f"You paid: ${self.cash} successfully."
        else:
            return f"You paid: ${self.cash} successfully and your change is: ${self.vueltas:.2f}"


class Fastfood(Menuitem):
    def __init__(self, name):
        super().__init__(name)
        prices = {"hamburguer": 15.99, "pizza": 9.99, "hotdog": 7.99, "salchipapa": 10.99}
        self.set_price(prices.get(name, 0))


class Desserts(Menuitem):
    def __init__(self, name):
        super().__init__(name)
        prices = {"cake": 6.99, "icecream": 1.99, "sundae": 6.99}
        self.set_price(prices.get(name, 0))


class Drinks(Menuitem):
    def __init__(self, name):
        super().__init__(name)
        prices = {"soda": 4.99, "juice": 3.99, "smoothie": 8.99, "water": 1.99}
        self.set_price(prices.get(name, 0))


class Proteins(Menuitem):
    def __init__(self, name):
        super().__init__(name)
        prices = {"chicken": 13.99, "fish": 14.99, "meat": 13.99}
        self.set_price(prices.get(name, 0))


class OrderIterator:
    """Iterator class for the Order, allows looping over items."""
    def __init__(self, items):
        self._items = items
        self._index = 0

    def __iter__(self):
        return self

    def __next__(self):
        if self._index < len(self._items):
            item = self._items[self._index]
            self._index += 1
            return item
        else:
            raise StopIteration


class Order:
    menu = [
        Fastfood("hamburguer"), Fastfood("pizza"), Fastfood("hotdog"),
        Fastfood("salchipapa"), Desserts("cake"), Desserts("icecream"),
        Desserts("sundae"), Drinks("soda"), Drinks("juice"),
        Drinks("smoothie"), Drinks("water"), Proteins("chicken"),
        Proteins("fish"), Proteins("meat")
    ]

    def __init__(self):
        self.menuitems = []

    def add_item(self, menuitem):
        menu_names = [item.get_name() for item in self.menu]
        if menuitem.get_name() not in menu_names:
            print(f"'{menuitem.get_name()}' is not on the menu. Please enter a valid item.")
            return False
        else:
            self.menuitems.append(menuitem)
            return True

    def take_order(self):
        return sum(item.get_price() for item in self.menuitems)

    def print_menu(self):
        print("Menu:")
        for item in self.menu:
            print(item)
        print("IF YOUR ORDER IS MORE THAN 50 DOLLARS, YOU GET A 2% DISCOUNT.\n"
              "ALSO, IF YOU PAY WITH CASH, YOU GET AN 8% DISCOUNT.\n")

    def print_receipt(self):
        print("This is your order receipt:")
        for item in self:
            print(f"- {item.get_name()}: ${item.get_price():.2f}")
        total_price = self.take_order()
        if total_price > 60:
            total_price *= 0.98
        print(f"Total: ${total_price:.2f}")
        return total_price

    def __iter__(self):
        return OrderIterator(self.menuitems)


# Main execution
if __name__ == "__main__":
    order = Order()
    order.print_menu()

    try:
        # Ordering Fastfood
        fast_food = input("Do you want to order Fast Food? (yes/no): ").lower()
        if fast_food == "yes":
            fast_food_quantity = int(input("How many? "))
            for _ in range(fast_food_quantity):
                while True:
                    order_food = input("What do you want to order: ").lower()
                    if order.add_item(Fastfood(order_food)):
                        break
        
        # Ordering Drinks
        drinks = input("Do you want to order Drinks? (yes/no): ").lower()
        if drinks == "yes":
            drinks_quantity = int(input("How many? "))
            for _ in range(drinks_quantity):
                while True:
                    order_drink = input("What do you want to order: ").lower()
                    if order.add_item(Drinks(order_drink)):
                        break

        # Ordering Desserts
        desserts = input("Do you want to order Desserts? (yes/no): ").lower()
        if desserts == "yes":
            desserts_quantity = int(input("How many? "))
            for _ in range(desserts_quantity):
                while True:
                    order_dessert = input("What do you want to order: ").lower()
                    if order.add_item(Desserts(order_dessert)):
                        break

        # Ordering Proteins
        proteins = input("Do you want to order Proteins? (yes/no): ").lower()
        if proteins == "yes":
            proteins_quantity = int(input("How many? "))
            for _ in range(proteins_quantity):
                while True:
                    order_protein = input("What do you want to order: ").lower()
                    if order.add_item(Proteins(order_protein)):
                        break

        # Print receipt
        total_price = order.print_receipt()

        # Payment process
        paying = input("Do you want to pay with cash or card? ").lower()
        if paying in ["cash", "efectivo"]:
            print("You get an 8% discount for paying with cash.")
            total_price *= 0.92
            cash = int(input("Enter the amount of cash you are paying with: "))
            payment = Cash(cash, total_price)
            if payment.cambio():
                print(payment.pagar())
        elif paying in ["card", "tarjeta"]:
            tipe = input("Enter card type (debit/credit): ").lower()
            number = input("Enter card number: ")
            cvc = input("Enter card CVC: ")
            card = Card(total_price, tipe, number, cvc)
            print(card.pagar())
        else:
            print("Invalid payment method.")

    except ValueError as ve:
        print(f"Error: Invalid input. {ve}")
    except Exception as e:
        print(f"An unexpected error occurred: {e}")

    # Iterating over the order
    print("\nIterating over your order items:")
    for item in order:
        print(f"Item: {item.get_name()}, Price: ${item.get_price():.2f}")
```
