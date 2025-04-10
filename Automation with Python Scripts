import os
import shutil
from pathlib import Path
import tkinter as tk
from tkinter import filedialog, messagebox, ttk

# Define file type categories and their target folders
FILE_TYPES = {
    "Images": [".jpg", ".jpeg", ".png", ".gif", ".bmp"],
    "PDFs": [".pdf"],
    "Text": [".txt", ".docx", ".md"],
    "Videos": [".mp4", ".mkv", ".avi"],
    "Other": []  # Catch-all for unlisted extensions
}

class FileOrganizerApp:
    def __init__(self, root):
        self.root = root
        self.root.title("File Organizer")
        self.root.geometry("600x450")
        self.root.configure(bg="#f0f4f8")  # Light gray-blue background
        self.root.resizable(False, False)  # Fixed size for consistency

        # Source directory variable
        self.source_dir = tk.StringVar(value=str(Path.home() / "Downloads"))

        # Build the GUI
        self.create_widgets()

    def create_widgets(self):
        """Set up the GUI with a modern, attractive design."""
        # Header
        header_frame = tk.Frame(self.root, bg="#4a90e2", pady=10)  # Blue header
        header_frame.pack(fill="x")
        tk.Label(header_frame, text="File Organizer", font=("Helvetica", 20, "bold"), 
                 fg="white", bg="#4a90e2").pack()

        # Main content frame
        content_frame = tk.Frame(self.root, bg="#f0f4f8", padx=20, pady=20)
        content_frame.pack(expand=True, fill="both")

        # Folder selection section
        tk.Label(content_frame, text="Select Folder to Organize:", 
                 font=("Helvetica", 12), bg="#f0f4f8").grid(row=0, column=0, pady=5, sticky="w")
        
        entry = ttk.Entry(content_frame, textvariable=self.source_dir, width=50)
        entry.grid(row=1, column=0, padx=(0, 10), pady=5)
        
        browse_btn = tk.Button(content_frame, text="Browse", command=self.browse_folder, 
                               bg="#50c878", fg="white", font=("Helvetica", 10, "bold"), 
                               relief="flat", padx=10, pady=5)
        browse_btn.grid(row=1, column=1, pady=5)

        # Organize button
        organize_btn = tk.Button(content_frame, text="Organize Files", command=self.organize_files, 
                                 bg="#ff6f61", fg="white", font=("Helvetica", 12, "bold"), 
                                 relief="flat", padx=20, pady=10)
        organize_btn.grid(row=2, column=0, columnspan=2, pady=20)

        # Output log section
        tk.Label(content_frame, text="Activity Log:", font=("Helvetica", 12), 
                 bg="#f0f4f8").grid(row=3, column=0, columnspan=2, sticky="w")
        
        self.log = tk.Text(content_frame, height=12, width=60, font=("Courier", 10), 
                           bg="white", fg="#333", relief="flat", borderwidth=2)
        self.log.grid(row=4, column=0, columnspan=2, pady=5)

        # Style buttons on hover
        for btn, hover_color in [(browse_btn, "#45b066"), (organize_btn, "#e65b50")]:
            btn.bind("<Enter>", lambda e, c=hover_color: btn.config(bg=c))
            btn.bind("<Leave>", lambda e, c=btn["bg"]: btn.config(bg=c))

    def browse_folder(self):
        """Open a dialog to select the source folder."""
        folder = filedialog.askdirectory(initialdir=self.source_dir.get(), title="Select Folder to Organize")
        if folder:
            self.source_dir.set(folder)
            self.log.insert(tk.END, f"Selected folder: {folder}\n")
            self.log.see(tk.END)

    def create_folders(self, base_dir):
        """Create subfolders if they don’t exist."""
        for folder in FILE_TYPES.keys():
            folder_path = os.path.join(base_dir, folder)
            if not os.path.exists(folder_path):
                os.makedirs(folder_path)

    def get_category(self, extension):
        """Determine the category for a file based on its extension."""
        for category, extensions in FILE_TYPES.items():
            if extension.lower() in extensions:
                return category
        return "Other"

    def organize_files(self):
        """Move files to their respective folders and log the process."""
        source_dir = self.source_dir.get()
        if not os.path.exists(source_dir):
            messagebox.showerror("Error", "Please select a valid folder!", parent=self.root)
            return

        self.log.delete(1.0, tk.END)  # Clear previous log
        self.log.insert(tk.END, f"Starting organization in {source_dir}...\n")
        self.root.update()

        self.create_folders(source_dir)

        for filename in os.listdir(source_dir):
            file_path = os.path.join(source_dir, filename)

            if os.path.isdir(file_path) or filename.startswith("."):
                continue

            _, extension = os.path.splitext(filename)

            if extension:
                category = self.get_category(extension)
                target_dir = os.path.join(source_dir, category)
                target_path = os.path.join(target_dir, filename)

                counter = 1
                while os.path.exists(target_path):
                    name, ext = os.path.splitext(filename)
                    target_path = os.path.join(target_dir, f"{name}_{counter}{ext}")
                    counter += 1

                shutil.move(file_path, target_path)
                self.log.insert(tk.END, f"Moved {filename} to {category}/\n")
                self.log.see(tk.END)  # Auto-scroll to the latest entry
                self.root.update()

        self.log.insert(tk.END, "Organization complete!\n")
        self.log.see(tk.END)
        messagebox.showinfo("Success", "Files have been organized!", parent=self.root)

if __name__ == "__main__":
    root = tk.Tk()
    app = FileOrganizerApp(root)
    root.mainloop()