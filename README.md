#include <iostream>
#include <string>

using namespace std;

class Product {
public:
    int id;
    string name;
    double price;

    Product() : id(0), name(""), price(0.0) {}
    Product(int pid, string pname, double pprice) {
        id = pid;
        name = pname;
        price = pprice;
    }

    void displayProduct() const {
        cout << id << ". " << name << " - Rs." << price << endl;
    }
};

class CartItem {
public:
    Product product;
    int quantity;

    CartItem() : quantity(0) {}
    CartItem(Product p, int q) {
        product = p;
        quantity = q;
    }

    double getTotalPrice() const {
        return product.price * quantity;
    }

    void displayCartItem() const {
        cout << product.name << " x " << quantity << " = Rs." << getTotalPrice() << endl;
    }
};

void displayWelcome() {
    cout << "==============================" << endl;
    cout << "     WELCOME TO SHOP EASY     " << endl;
    cout << "==============================" << endl;
}

void displayMenu() {
    cout << "\n1. View Products";
    cout << "\n2. Add to Cart";
    cout << "\n3. View Cart";
    cout << "\n4. Checkout";
    cout << "\n5. Exit";
    cout << "\nEnter your choice: ";
}

void viewProducts(const Product products[], int productCount) {
    cout << "\nAvailable Products:\n";
    for (int i = 0; i < productCount; i++) {
        products[i].displayProduct();
    }
}

int addToCart(CartItem cart[], int cartCount, const Product products[], int productCount) {
    if (cartCount >= 10) {
        cout << "Your cart is full! Cannot add more items.\n";
        return cartCount;
    }

    int pid, qty;
    cout << "\nEnter Product ID to add: ";
    cin >> pid;
    cout << "Enter Quantity: ";
    cin >> qty;

    int foundIndex = -1;
    for (int i = 0; i < productCount; i++) {
        if (products[i].id == pid) {
            foundIndex = i;
            break;
        }
    }

    if (foundIndex != -1) {
        cart[cartCount] = CartItem(products[foundIndex], qty);
        cout << products[foundIndex].name << " added to cart.\n";
        return cartCount + 1;
    } else {
        cout << "Invalid Product ID!\n";
        return cartCount;
    }
}

void viewCart(const CartItem cart[], int cartCount) {
    if (cartCount == 0) {
        cout << "\nYour cart is empty!\n";
    } else {
        cout << "\nYour Cart:\n";
        double total = 0;
        for (int i = 0; i < cartCount; i++) {
            cart[i].displayCartItem();
            total += cart[i].getTotalPrice();
        }
        cout << "----------------------------\n";
        cout << "Total Amount: Rs." << total << endl;
    }
}

int checkout(const CartItem cart[], int cartCount) {
    if (cartCount == 0) {
        cout << "\nYour cart is empty! Add items first.\n";
    } else {
        double total = 0;
        cout << "\nCheckout Summary:\n";
        for (int i = 0; i < cartCount; i++) {
            cart[i].displayCartItem();
            total += cart[i].getTotalPrice();
        }
        cout << "----------------------------\n";
        cout << "Total Bill: Rs." << total << endl;
        cout << "Thank you for shopping with us!\n";
    }

    return 0;
}

int main() {
    Product products[5] = {
        Product(1, "Laptop", 55000),
        Product(2, "Smartphone", 20000),
        Product(3, "Headphones", 1500),
        Product(4, "Keyboard", 700),
        Product(5, "Mouse", 500)
    };
    int productCount = 5;

    CartItem cart[10];
    int cartCount = 0;
    int choice;

    displayWelcome();

    while (true) {
        displayMenu();
        cin >> choice;

        switch (choice) {
            case 1:
                viewProducts(products, productCount);
                break;
            case 2:
                cartCount = addToCart(cart, cartCount, products, productCount);
                break;
            case 3:
                viewCart(cart, cartCount);
                break;
            case 4:
                cartCount = checkout(cart, cartCount); 
                break;
            case 5:
                cout << "\nThank you! Visit again.\n";
                return 0;
            default:
                cout << "Invalid choice! Try again.\n";
        }
    }
}
