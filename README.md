#include <iostream>
#include <string>
#include <fstream>

using namespace std;

// --- 1. FUNCTION OVERLOADING ---
void displayHeader(string title) {
    cout << "\n======================================\n";
    cout << "      " << title << "\n";
    cout << "======================================\n";
}

void displayHeader(string title, string subtitle) {
    displayHeader(title); // This one calls the other one!
    cout << "--- " << subtitle << " ---\n";
}

// --- 2. INHERITANCE (Base Class) ---
class Item {
public:
    int id;
    string name;
};

// --- 3. FRIEND FUNCTION & 4. OPERATOR OVERLOADING ---
class Product : public Item {
private:
    double price; 

public:
    Product() {}
    Product(int pid, string pname, double pprice) {
        id = pid;
        name = pname;
        this->price = pprice;
    }

    double getPrice() {
        return price;
    }

    void show() {
        cout << id << ". " << name << " - Rs." << price << endl;
    }

    // --- 4. OPERATOR OVERLOADING ---
    bool operator==(const Product& other) {
        return this->id == other.id;
    }
    
    // --- 3. FRIEND FUNCTION (Declaration) ---
    friend void showTax(Product p);
};

// --- 3. FRIEND FUNCTION (Definition) ---
void showTax(Product p) {
    double tax = p.price * 0.18; // Accessing private 'price'
    cout << "Tax (18%) for " << p.name << " is: Rs." << tax << endl;
}

// --- CartItem Class (Modified) ---
class CartItem {
public:
    Product product;
    int quantity;

    CartItem() {}
    CartItem(Product p, int q) {
        product = p;
        quantity = q;
    }

    double totalPrice() {
        return product.getPrice() * quantity;
    }

    void showItem() {
        cout << product.name << " x " << quantity << " = Rs." << totalPrice() << endl;
    }

    void saveToFile(ofstream &file) {
        file << product.name << " x " << quantity << " = Rs." << totalPrice() << endl;
    }
};

// --- 5. TEMPLATE ---
template <typename T>
void showArray(T arr[], int size) {
    for (int i = 0; i < size; i++) {
        arr[i].show();
    }
}

int main() {
    // This counter will be used for Bill IDs
    static int billCounter = 101; 

    Product products[5] = {
        Product(1, "Laptop", 55000),
        Product(2, "Smartphone", 20000),
        Product(3, "Headphones", 1500),
        Product(4, "Keyboard", 700),
        Product(5, "Mouse", 500)
    };

    CartItem cart[10];
    int cartCount = 0;
    int choice, qty;

    displayHeader("SHOP EASY STORE");

    while (true) {
        cout << "\n1. View Products";
        cout << "\n2. Add to Cart";
        cout << "\n3. View Cart";
        cout << "\n4. Checkout";
        cout << "\n5. View Saved Bills";
        cout << "\n6. Demo Friend Function";
        cout << "\n7. Exit";
        cout << "\nEnter your choice: ";
        cin >> choice;

        if (choice == 1) {
            displayHeader("Available Products", "All Items");
            showArray(products, 5);
        }  
        else if (choice == 2) {
            int pid;
            cout << "\nEnter Product ID: ";
            cin >> pid;
            cout << "Enter Quantity: ";
            cin >> qty;

            if (pid >= 1 && pid <= 5) {
                cart[cartCount++] = CartItem(products[pid - 1], qty);
                cout << products[pid - 1].name << " added to cart.\n";
            } else {
                cout << "Invalid Product ID!\n";
            }
        }  
        else if (choice == 3) {
            if (cartCount == 0) {
                cout << "\nYour cart is empty!\n";
            } else {
                displayHeader("Your Cart");
                double total = 0;
                for (int i = 0; i < cartCount; i++) {
                    cart[i].showItem();
                    total += cart[i].totalPrice();
                }
                cout << "----------------------------\n";
                cout << "Total: Rs." << total << endl;
            }
        }  
        else if (choice == 4) {
            if (cartCount == 0) {
                cout << "\nYour cart is empty!\n";
            } else {
                // --- 6. DYNAMIC MEMORY ALLOCATION ---
                string* billId = new string; // 1. Allocate memory
                
                // 2. Use the memory (using the billCounter)
                *billId = "BILL-" + to_string(billCounter++); 
                
                cout << "\nCheckout Summary (Bill ID: " << *billId << ")\n";
                
                ofstream file("bill.txt", ios::app);
                file << "\n==============================" << endl;
                file << "         SHOP EASY BILL         \n";
                file << "Bill ID: " << *billId << endl;
                file << "==============================" << endl;

                double total = 0;
                for (int i = 0; i < cartCount; i++) {
                    cart[i].showItem();
                    cart[i].saveToFile(file);
                    total += cart[i].totalPrice();
                }

                file << "----------------------------" << endl;
                file << "Total: Rs." << total << endl;
                file << "==============================\n" << endl;
                file.close();
                
                cout << "----------------------------\n";
                cout << "Total Bill: Rs." << total << endl;
                
                // 3. Free the memory
                delete billId;

                cout << "Bill saved to file.\n";
                cout << "Thank you for shopping!\n";
                cartCount = 0;
            }
        }  
        else if (choice == 5) {
            displayHeader("Saved Bills");
            ifstream file("bill.txt");
            if (!file) {
                cout << "\nNo bills found.\n";
            } else {
                string line;
                while (getline(file, line)) {
                    cout << line << endl;
                }
                file.close();
            }
        }  
        else if (choice == 6) {
            displayHeader("Friend Function Demo");
            cout << "Running friend function 'showTax' on 'Laptop':\n";
            showTax(products[0]);
        }
        else if (choice == 7) {
            cout << "\nThank you! Visit again.\n";
            break;
        }  
        else {
            cout << "Invalid choice! Try again.\n";
        }
    }

    return 0;
}
