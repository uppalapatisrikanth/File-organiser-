import os
import shutil
import logging
import time


logging.basicConfig(filename='file_organizer.log', level=logging.INFO,
                    format='%(asctime)s - %(levelname)s - %(message)s')

WATCH_DIRECTORY = os.path.expanduser("~/Downloads")  # Adjust this path if necessary


FILE_TYPES = {
    "Images": ['jpg', 'jpeg', 'png', 'gif', 'bmp', 'svg'],
    "Documents": ['doc', 'docx', 'pdf', 'txt'],
    "Spreadsheets": ['xls', 'xlsx', 'csv'],
    "Presentations": ['ppt', 'pptx'],
    "Videos": ['mp4', 'mkv', 'avi', 'mov'],
    "Audio": ['mp3', 'wav', 'aac'],
    "Archives": ['zip', 'rar', '7z', 'tar', 'gz'],
}

def organize_file(file_path, file_name, extension):
    """Moves the file to the appropriate folder based on its extension."""
    folder_name = "Others"  # Default folder for unknown file types
    for category, extensions in FILE_TYPES.items():
        if extension in extensions:
            folder_name = category
            break

    dest_folder = os.path.join(WATCH_DIRECTORY, folder_name)
    if not os.path.exists(dest_folder):
        os.makedirs(dest_folder)
        logging.info(f"Created folder: {dest_folder}")

  
    try:
        shutil.move(file_path, os.path.join(dest_folder, file_name))
        logging.info(f"Moved: {file_name} to {dest_folder}/")
        print(f"Moved: {file_name} to {dest_folder}/")
    except Exception as e:
        logging.error(f"Error moving file {file_name}: {e}")
        print(f"Error moving file {file_name}: {e}")

def organize_existing_files():
    """Organizes files already in the Downloads folder."""
    for file_name in os.listdir(WATCH_DIRECTORY):
        file_path = os.path.join(WATCH_DIRECTORY, file_name)
        if os.path.isfile(file_path):
            _, extension = os.path.splitext(file_name)
            extension = extension[1:].lower()  # Remove the dot and convert to lowercase
            logging.info(f"Organizing existing file: {file_name} with extension: {extension}")
            organize_file(file_path, file_name, extension)

def start_monitoring():
    """Continuously checks the Downloads directory for new files."""
    already_processed = set(os.listdir(WATCH_DIRECTORY))  # Track already processed files
    logging.info(f"Started monitoring {WATCH_DIRECTORY}")
    print(f"Started monitoring {WATCH_DIRECTORY} for new files...")

    while True:
        current_files = set(os.listdir(WATCH_DIRECTORY))  # Get current files in the directory
        new_files = current_files - already_processed  # Determine new files

        for file_name in new_files:
            file_path = os.path.join(WATCH_DIRECTORY, file_name)
            if os.path.isfile(file_path):
                _, extension = os.path.splitext(file_name)
                extension = extension[1:].lower()  
                organize_file(file_path, file_name, extension)

        already_processed.update(new_files) 
        time.sleep(60)  

if name == "main":
  
    organize_existing_files()
   
    start_monitoring()
