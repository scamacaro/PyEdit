"""
Sanyerlis Camacaro - CCSC285 - Sancamac@uat.edu Assignment:
Assignment 3.1: Create Your Own Python Code Editor

"PyEdit"

This code demonstrates how to:

1. Create a Text Editor.
2. Adding Menus.
3. Add a top-level menu to your application with "File" and "Edit" options.
4. Enhancing the Text Editor.
5. Status bar at the bottom of the screen.
"""
# Create an Editor called "PyEdit"
# Import Tkinter as our GUI library
import tkinter as tk
from tkinter import filedialog, Text

# Tkinter is as easy as 123
# 1. Create all your GUI elements called widgets
# 2. Configure the widgets by setting properties
# 3. Add your widgets to the window

# Create a class that is our main instance of the application
class TextEditor:
    # Initialize the class using a constructor
    def __init__(self, root):
        # Create a root window
        self.root = root
        # Set the title of the window
        self.root.title("PyEdit")
        # Create a widget where the user will edit code
        # SET LOTS OF properties here! This give a great look and feel an UX
        self.textarea = Text(self.root, bg="dark blue", fg="white", insertbackground="white", selectbackground="green", selectforeground="white", undo=True, autoseparators=True, maxundo=-1, padx=10, pady=10, wrap=tk.WORD, font=("Consolas", 11))
        
        # Add a status bar at the bottom of the application
        self.status_bar = tk.Label(self.root, text="Line 1", bd=1, relief=tk.SUNKEN, anchor=tk.W)
        self.status_bar.pack(side=tk.BOTTOM, fill=tk.X)
        self.textarea.bind("<KeyRelease>", self.update_status_bar)  # Binding the update_status_bar function to every key release
        
        # Make the editor fill the entire window
        self.textarea.pack(fill="both", expand=True)
        # Initialize the current file variable,
        # this will keep track of the current file being edited
        self.current_file = "no file"

        # Create a menu bar using a widget
        self.menu = tk.Menu(root)
        # Add a file menu to the window menu bar
        self.root.config(menu=self.menu)

        # Create our sub menus
        # This will be our file menu
        self.file_menu = tk.Menu(self.menu)
        # Add our file menu to the window menu bar
        self.menu.add_cascade(label="File", menu=self.file_menu)
        # Add some more options to our file menu.
        # We will add a command to open a file
        self.file_menu.add_command(label="Open", command=self.open_file)
        # Add a command to save a file
        self.file_menu.add_command(label="Save", command=self.save_file)
        # Add a command to save a file as
        self.file_menu.add_command(label="Save As", command=self.save_file_as)

        # Adding the "Edit" menu
        self.edit_menu = tk.Menu(self.menu)
        self.menu.add_cascade(label="Edit", menu=self.edit_menu)
        # Adding "Copy", "Cut", and "Paste" options under the "Edit" menu
        self.edit_menu.add_command(label="Copy", command=lambda: self.textarea.event_generate('<Control c>'))
        self.edit_menu.add_command(label="Cut", command=lambda: self.textarea.event_generate('<Control x>'))
        self.edit_menu.add_command(label="Paste", command=lambda: self.textarea.event_generate('<Control v>'))

    # Create our file methods. NOTE! These are inside and part of the class.
    # We will pass in self so we can access the class variables
    def open_file(self):
        file_path = filedialog.askopenfilename(defaultextension=".txt", filetypes=[("All files", "*.*"), ("Text Files", "*.txt"), ("Python Scripts", "*.py"), ("HTML Documents", "*.html"), ("C++ Documents", "*.cpp"), ("C Documents", "*.c"), ("Rust Documents", "*.rs")])
        if file_path == "":
            file_path = None
        else:
            self.root.title(f"PyEdit - {file_path}")
            self.textarea.delete(1.0, tk.END)
            with open(file_path, "r") as file:
                self.textarea.insert(1.0, file.read())
            self.current_file = file_path

    # Save the current file the user is editing
    def save_file(self):
        # If the current file is no file, then we will save the file as 
        if self.current_file == "no file":
            self.save_file_as()
            # If the current file the user is editing
            # is an existing file, then we will save the file
        else:
            with open(self.current_file, "w") as file:  # Fix: replaced "self.current" with "self.current_file"
                file.write(self.textarea.get(1.0, tk.END))

    # Save the current file our use is editing as a new file.
    def save_file_as(self):
        # Open a file dialog and get the file path
        file_path = filedialog.asksaveasfilename(defaultextension=".txt", filetypes=[("All Files", "*.*"), ("Text Files", "*.txt"), ("Python Scripts", "*.py"), ("HTML Documents", "*.html"), ("C++ Documents", "*.cpp"), ("C Documents", "*.c"), ("Rust Documents", "*.rs")])
        # If the user cancels the save file dialog, then we will set the file path to None
        if file_path == "":
            file_path = None
        else:
            # If the user saves the file, then we will save the file 
            with open(file_path, "w") as file:
                # Write the contents of the text area to the file
                file.write(self.textarea.get(1.0, tk.END))
            self.root.title(f"PyEdit - {file_path}")
            self.current_file = file_path

    def update_status_bar(self, event=None):
        # Function to update the status bar to show the current line number
        line = self.textarea.index(tk.INSERT).split(".")[0]
        self.status_bar.config(text=f"Line {line}")

# Create the main starting point of the application
if __name__ == "__main__":
    # Create a root window
    root = tk.Tk()
    # Set the size of the window
    root.geometry("600x600")
    # Create an instance of the TextEditor class
    TextEditor(root)
    # Run the main loop of the application
    # Most important line of code in the entire GUI application
    # If this is missing, then the application will not show a window
    root.mainloop()
