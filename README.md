# API-INTEGRATION-AND-DATA-VISUAlization
import tkinter as tk
from tkinter import messagebox
from tkinter import ttk

inventory = {}
users = {"admin": "password123"}
# Authentication screen
def login():
    def validate_user():
        username = username_entry.get()
        password = password_entry.get()
        if username in users and users[username] == password:
            auth_window.destroy()
            main_screen()
        else:
            messagebox.showerror("Error", "Invalid username or password")

    auth_window = tk.Tk()
    auth_window.title("Login")
    tk.Label(auth_window, text="Username").grid(row=0, column=0, padx=10, pady=10)
    username_entry = tk.Entry(auth_window)
    username_entry.grid(row=0, column=1)

    tk.Label(auth_window, text="Password").grid(row=1, column=0, padx=10, pady=10)
    password_entry = tk.Entry(auth_window, show="*")
    password_entry.grid(row=1, column=1)

    tk.Button(auth_window, text="Login", command=validate_user).grid(row=2, column=0, columnspan=2, pady=10)
    auth_window.mainloop()

# Main inventory screen
def main_screen():
    def add_product():
        def save_product():
            product_id = id_entry.get()
            name = name_entry.get()
            quantity = int(quantity_entry.get())
            price = float(price_entry.get())

            if product_id in inventory:
                messagebox.showerror("Error", "Product ID already exists")
            else:
                inventory[product_id] = {"name": name, "quantity": quantity, "price": price}
                messagebox.showinfo("Success", f"Product '{name}' added successfully")
                add_window.destroy()
                refresh_inventory()

        add_window = tk.Toplevel()
        add_window.title("Add Product")
        tk.Label(add_window, text="Product ID").grid(row=0, column=0, padx=10, pady=10)
        id_entry = tk.Entry(add_window)
        id_entry.grid(row=0, column=1)

        tk.Label(add_window, text="Product Name").grid(row=1, column=0, padx=10, pady=10)
        name_entry = tk.Entry(add_window)
        name_entry.grid(row=1, column=1)

        tk.Label(add_window, text="Quantity").grid(row=2, column=0, padx=10, pady=10)
        quantity_entry = tk.Entry(add_window)
        quantity_entry.grid(row=2, column=1)

        tk.Label(add_window, text="Price").grid(row=3, column=0, padx=10, pady=10)
        price_entry = tk.Entry(add_window)
        price_entry.grid(row=3, column=1)

        tk.Button(add_window, text="Save", command=save_product).grid(row=4, column=0, columnspan=2, pady=10)

    def edit_product():
        def save_changes():
            product_id = id_entry.get()
            if product_id not in inventory:
                messagebox.showerror("Error", "Product ID not found")
            else:
                name = name_entry.get()
                quantity = int(quantity_entry.get())
                price = float(price_entry.get())
                inventory[product_id] = {"name": name, "quantity": quantity, "price": price}
                messagebox.showinfo("Success", f"Product '{name}' updated successfully")
                edit_window.destroy()
                refresh_inventory()

        edit_window = tk.Toplevel()
        edit_window.title("Edit Product")
        tk.Label(edit_window, text="Product ID").grid(row=0, column=0, padx=10, pady=10)
        id_entry = tk.Entry(edit_window)
        id_entry.grid(row=0, column=1)

        tk.Label(edit_window, text="Product Name").grid(row=1, column=0, padx=10, pady=10)
        name_entry = tk.Entry(edit_window)
        name_entry.grid(row=1, column=1)

        tk.Label(edit_window, text="Quantity").grid(row=2, column=0, padx=10, pady=10)
        quantity_entry = tk.Entry(edit_window)
        quantity_entry.grid(row=2, column=1)

        tk.Label(edit_window, text="Price").grid(row=3, column=0, padx=10, pady=10)
        price_entry = tk.Entry(edit_window)
        price_entry.grid(row=3, column=1)

        tk.Button(edit_window, text="Save", command=save_changes).grid(row=4, column=0, columnspan=2, pady=10)

    def delete_product():
        product_id = id_entry.get()
        if product_id in inventory:
            del inventory[product_id]
            messagebox.showinfo("Success", "Product deleted successfully")
            refresh_inventory()
        else:
            messagebox.showerror("Error", "Product ID not found")

    def low_stock_report():
        report_window = tk.Toplevel()
        report_window.title("Low Stock Report")
        tk.Label(report_window, text="Low Stock Products (Qty < 5):").grid(row=0, column=0, padx=10, pady=10)
        row = 1
        for product_id, details in inventory.items():
            if details["quantity"] < 5:
                tk.Label(report_window, text=f"{product_id}: {details['name']} - Qty: {details['quantity']}").grid(
                    row=row, column=0, padx=10, pady=5)
                row += 1

    def refresh_inventory():
        for row in inventory_tree.get_children():
            inventory_tree.delete(row)
        for product_id, details in inventory.items():
            inventory_tree.insert("", "end",
                                  values=(product_id, details["name"], details["quantity"], details["price"]))

    #  GUI
    root = tk.Tk()
    root.title("Inventory Management System")
    # Buttons
    tk.Button(root, text="Add Product", command=add_product).grid(row=0, column=0, padx=10, pady=10)
    tk.Button(root, text="Edit Product", command=edit_product).grid(row=0, column=1, padx=10, pady=10)
    tk.Button(root, text="Delete Product", command=delete_product).grid(row=0, column=2, padx=10, pady=10)
    tk.Button(root, text="Low Stock Report", command=low_stock_report).grid(row=0, column=3, padx=10, pady=10)

    columns = ("Product ID", "Name", "Quantity", "Price")
    inventory_tree = ttk.Treeview(root, columns=columns, show="headings")
    for col in columns:
        inventory_tree.heading(col, text=col)
        inventory_tree.column(col, width=150)
    inventory_tree.grid(row=1, column=0, columnspan=4, padx=10, pady=10)

    refresh_inventory()
    root.mainloop()
login()
