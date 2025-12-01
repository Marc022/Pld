#include <iostream>
#include <iomanip>
#include <vector>
#include <string>
#include <limits>

using namespace std;

struct Item {
    int id;
    string name;
    double price;
    int stock;
};

struct CartItem {
    int id;     // item id
    int qty;    // quantity in cart
};

void showHeader() {
    cout <<
R"(  +===========================================+
  |         CRAMZ REVUP ACCESSORIES            |
  |  Power Pipes • Shocks • Lights • Mods      |
  +===========================================+

        |====|====|====|====|====|====|====|
        0   1   2   3   4   5   6   7   8  9  RPM

)";
}

void clearInput() {
    cin.clear();
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
}

int readInt(const string& prompt, int minV, int maxV) {
    int x;
    while (true) {
        cout << prompt;
        if (cin >> x && x >= minV && x <= maxV) return x;
        cout << "Invalid input. Enter " << minV << " to " << maxV << ".\n";
        clearInput();
    }
}

int readIntMin(const string& prompt, int minV) {
    int x;
    while (true) {
        cout << prompt;
        if (cin >> x && x >= minV) return x;
        cout << "Invalid input. Enter >= " << minV << ".\n";
        clearInput();
    }
}

int findItemIndex(const vector<Item>& items, int id) {
    for (int i = 0; i < (int)items.size(); i++)
        if (items[i].id == id) return i;
    return -1;
}

int findCartIndex(const vector<CartItem>& cart, int id) {
    for (int i = 0; i < (int)cart.size(); i++)
        if (cart[i].id == id) return i;
    return -1;
}

void showInventory(const vector<Item>& items) {
    cout << "\n=== CRAMZ REVUP ACCESSORIES - INVENTORY ===\n";

    cout << left
         << setw(5)  << "ID"
         << setw(40) << "Item"
         << right
         << setw(12) << "Price"
         << setw(10) << "Stock"
         << "\n";

    cout << string(67, '-') << "\n";

    for (const auto& it : items) {
        cout << left
             << setw(5)  << it.id
             << setw(40) << it.name
             << right
             << setw(12) << fixed << setprecision(2) << it.price
             << setw(10) << it.stock
             << "\n";
    }

    cout << endl;
}

void showCart(const vector<Item>& items, const vector<CartItem>& cart) {
    cout << "\n=== CRAMZ REVUP ACCESSORIES - YOUR CART ===\n";
    if (cart.empty()) {
        cout << "(empty cart)\n\n";
        return;
    }

    cout << left
         << setw(5)  << "ID"
         << setw(40) << "Item"
         << right
         << setw(7)  << "Qty"
         << setw(12) << "Price"
         << setw(12) << "Subtotal" << "\n";

    cout << string(76, '-') << "\n";

    double total = 0.0;
    for (auto &c : cart) {
        int idx = findItemIndex(items, c.id);
        if (idx == -1) continue;

        double sub = items[idx].price * c.qty;
        total += sub;

        cout << left
             << setw(5)  << items[idx].id
             << setw(40) << items[idx].name
             << right
             << setw(7)  << c.qty
             << setw(12) << fixed << setprecision(2) << items[idx].price
             << setw(12) << fixed << setprecision(2) << sub
             << "\n";
    }

    cout << string(76, '-') << "\n";
    cout << right << setw(64) << "TOTAL: "
         << setw(12) << fixed << setprecision(2) << total << "\n\n";
}

void addToCart(vector<Item>& items, vector<CartItem>& cart) {
    // inventory is already shown in main loop now

    int id = readInt("Enter item ID to add: ", items.front().id, items.back().id);
    int idx = findItemIndex(items, id);
    if (idx == -1) {
        cout << "Item not found.\n";
        return;
    }

    if (items[idx].stock == 0) {
        cout << "Item is out of stock.\n";
        return;
    }

    int qty = readIntMin("Enter quantity: ", 1);

    if (qty > items[idx].stock) {
        cout << "Only " << items[idx].stock << " available. Adding max.\n";
        qty = items[idx].stock;
    }

    int cidx = findCartIndex(cart, id);
    if (cidx == -1) cart.push_back({id, qty});
    else {
        int newQty = cart[cidx].qty + qty;
        if (newQty > items[idx].stock) {
            newQty = items[idx].stock;
            cout << "Cart adjusted to stock max: " << newQty << "\n";
        }
        cart[cidx].qty = newQty;
    }

    cout << "Added to cart.\n";
}

void removeFromCart(vector<Item>& items, vector<CartItem>& cart) {
    showCart(items, cart);
    if (cart.empty()) return;

    int id = readInt("Enter item ID to remove: ", items.front().id, items.back().id);
    int cidx = findCartIndex(cart, id);

    if (cidx == -1) {
        cout << "That item isn't in your cart.\n";
        return;
    }

    int qty = readIntMin("Quantity to remove: ", 1);

    if (qty >= cart[cidx].qty) {
        cart.erase(cart.begin() + cidx);
        cout << "Item removed from cart.\n";
    } else {
        cart[cidx].qty -= qty;
        cout << "Quantity reduced in cart.\n";
    }
}

void checkout(vector<Item>& items, vector<CartItem>& cart) {
    showCart(items, cart);
    if (cart.empty()) return;

    // Validate stock
    for (auto &c : cart) {
        int idx = findItemIndex(items, c.id);
        if (idx == -1 || c.qty > items[idx].stock) {
            cout << "Checkout failed: stock is insufficient.\n";
            return;
        }
    }

    double total = 0;
    for (auto &c : cart) {
        int idx = findItemIndex(items, c.id);
        total += items[idx].price * c.qty;
    }

    cout << "Total bill: " << fixed << setprecision(2) << total << "\n";
    int confirm = readInt("Confirm checkout? (1=Yes, 2=No): ", 1, 2);
    if (confirm == 2) {
        cout << "Checkout cancelled.\n";
        return;
    }

    // Deduct stock
    for (auto &c : cart) {
        int idx = findItemIndex(items, c.id);
        items[idx].stock -= c.qty;
    }

    cart.clear();
    cout << "\n✅ Thank you for shopping at CRAMZ REVUP ACCESSORIES!\n"
         << "Your order is complete and inventory is updated.\n";
}

int main() {
    vector<Item> items = {
        {1, "Rcb Front Shock Mio i125",             4500.00, 5},
        {2, "Rcb Front Shock Click 125/Aerox 155", 6500.00, 12},
        {3, "Jvt Power Pipe M3/Click 125",          7000.00, 9},
        {4, "KOU Power Pipe",                       6500.00, 15},
        {5, "UMA Radiator Click/Aerox",             2000.00, 20}
    };

    vector<CartItem> cart;

    while (true) {
        showHeader();

        // ✅ Always show items when program runs/loops
        showInventory(items);

        cout << "What would you like to do today?\n";
        cout << "1) Add Item to Cart\n"
             << "2) View Cart\n"
             << "3) Remove Item from Cart\n"
             << "4) Checkout\n"
             << "5) Exit\n";

        int ch = readInt("Choose option: ", 1, 5);

        switch (ch) {
            case 1: addToCart(items, cart); break;
            case 2: showCart(items, cart); break;
            case 3: removeFromCart(items, cart); break;
            case 4: checkout(items, cart); break;
            case 5:
                cout << "Goodbye from Cramz RevUp Accessories!\n";
                return 0;
        }
        cout << "\n";
    }
}
