
!pip install cryptography
!pip install pyfiglet==0.7
!pip install lolcat
!pip install tqdm














from cryptography.fernet import Fernet
import pyfiglet
from termcolor import colored
import os
from tqdm import tqdm

print()

styled_text = pyfiglet.figlet_format('file  encryptor', font='doom')
colored_text = colored(styled_text, color='red',)
print(colored_text)

def generate_key():
    key = Fernet.generate_key()
    key_directory = input("Enter the directory path to save the key file: ")
    key_name = input("Enter the name of the key file: ")

    # Construct the full file path
    key_path = os.path.join(key_directory, key_name)

    # Check if the specified directory exists
    if not os.path.exists(key_directory):
        print("Error: Specified directory does not exist.")
        return

    # Storing the key in a file
    with open(key_path, 'wb') as filekey:
        filekey.write(key)
    print()

    print("Key generated and saved successfully at", key_path)

def encrypt_file_main():
    # Prompt user for key path
    key_path = input("Enter the path of the key: ")

    # Check if the specified key path is a directory
    if os.path.isdir(key_path):
        print("Error: Specified key path is a directory. Please provide a valid file path.")
        return

    # Check if the key file exists
    if not os.path.exists(key_path):
        print("Error: Specified key file does not exist. Please provide a valid key file.")
        return

    # Read the key from the file
    with open(key_path, 'rb') as key_file:
        key = key_file.read()

    # Using the provided key
    fernet = Fernet(key)

    # Prompt user for file path to encrypt
    file_path = input('Enter the path of the file to encrypt:')

    # Check if the specified file path is a directory
    if os.path.isdir(file_path):
        print("Error: Specified file path is a directory. Please provide a valid file path.")
        return

    # Check if the specified file exists
    if not os.path.exists(file_path):
        print("Error: Specified file does not exist. Please provide a valid file path.")
        return

    # Opening the original file to encrypt
    with open(file_path, 'rb') as file:
        original = file.read()

    # Encrypting the file
    encrypted = fernet.encrypt(original)

    # Prompt user for the directory path to save the encrypted file
    encrypted_dir = input('Enter the directory path to save the encrypted file: ')

    # Check if the specified directory exists
    if not os.path.exists(encrypted_dir):
        print("Error: Path does not exist. Please provide a valid path.")
        return

    # Construct the full file path for the encrypted file
    encrypted_file_path = os.path.join(encrypted_dir, os.path.basename(file_path) + '.encrypted')

    # Writing the encrypted data to the encrypted file with progress bar
    with open(encrypted_file_path, 'wb') as encrypted_file:
        # Create tqdm instance with total size of encrypted data
        progress_bar = tqdm(total=len(encrypted), unit='B', unit_scale=True, desc='Encrypting', dynamic_ncols=True)
        # Write data chunk by chunk with updating the progress bar
        for chunk in range(0, len(encrypted), 1024):
            encrypted_file.write(encrypted[chunk:chunk + 1024])
            progress_bar.update(1024)
        progress_bar.close()

    print()
    print('File encrypted successfully.')
    print()



def decrypt_file():
    encrypted_file_path = input('Enter the path of the encrypted file for decryption: ')
    key_path = input("Enter the path of the key: ")
    output_path = input("Enter the path where you want to save the decrypted file: ")
    
    # Reading the key
    with open(key_path, 'rb') as key_file:
        key = key_file.read()
    
    # Using the key for decryption
    fernet = Fernet(key)
    
    # Opening the encrypted file
    with open(encrypted_file_path, 'rb') as enc_file:
        encrypted = enc_file.read()
    
    # Decrypting the file
    decrypted = fernet.decrypt(encrypted)
    
    # Writing the decrypted data to a new file
    decrypted_file_name = os.path.splitext(os.path.basename(encrypted_file_path))[0]
    decrypted_file_path = os.path.join(output_path, decrypted_file_name)
    with open(decrypted_file_path, 'wb') as dec_file:
        dec_file.write(decrypted)

    print('File decrypted successfully and saved to:', decrypted_file_path)
    print()


while True:
    user_input = input(">> Enter 0 to generate key \n>> Enter 1 to encrypt file \n>> Enter 2 decrypt file \n>> Enter 3 exit:\n\nSelect option:  ")
    if user_input == '0':
        generate_key()
    elif user_input == '1':
        encrypt_file_main()
    elif user_input == '2':
        decrypt_file()
    elif user_input == '3':
        print("Exiting program.")
        break
    else:
        print("Invalid input. Please enter 0,1, 2, or 3.")
    print()