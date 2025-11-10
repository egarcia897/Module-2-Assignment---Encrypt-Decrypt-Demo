# Module-2-Assignment---Encrypt-Decrypt-Demo
Build simple application with user login and role-based access control. Demonstration of implementation of basic authentication and access restrictions based on role.  Python language to encrypt/decrypt a short message using symmetric and asymmetric method.

This program demonstrates symmetric (AES) and asymmetric (RSA) encryption.
User: 
  Mr.Emily (password: admin245), has full access to both encryption types.
  Andrew (password: user245), has access to symmetric encryption only
Shows keys, inputs, outputs, and strengths/weaknesses of eachh method.
Requirements: pip install cryptography

"""Estela Garcia
Date: 11/07/2025
Course: SDEV245
Assignment: Module 2: Encrypt/Decrypt Demo
Short Desc: Build simple application with user login and role-based access control.
Demonstration of implementation of basic authentication and access restrictions based on role. 
Python language to encrypt/decrypt a short message using symmetric and asymmetric method."""
from cryptography.fernet import Fernet
from cryptography.hazmat.primitives.asymmetric import rsa, padding
from cryptography.hazmat.primitives import hashes, serialization

#users and roles
users = {
    "Mr.Emily": {"password": "admin245", "role": "admin"},
    "Andrew": {"password": "user245", "role": "user"}
}
#login function
def login():
    username = input("Enter username: ")
    password = input("password:")

    if username == "Mr.Emily" and password == "admin245":
        print(f"Welcome {username}! Role: admin")
        return username, "admin"
    elif username == "Andrew" and password == "user245":
        print(f"Welcome {username}! Role: user")
        return username, "user"
    else: 
        print("Stranger danger! You shall not pass! User not found.")
        return None, None

def main():
    username, role = login()
    if not username:
        return 
    
# Symmetric Encryption
#key to be kept a secret

    key = Fernet.generate_key()
    cipher = Fernet(key)
    print (f"Generated Key: {key.decode()}")

    message = b"Pumpkin Pie ice cream is the best!."
    encrypted_message = cipher.encrypt(message)
    print(f"Encrypted Message: {encrypted_message.decode()}")

    decrypted_message = cipher.decrypt(encrypted_message)
    print(f"Decrypted Message: {decrypted_message.decode()}")
    print("\nStrengthes: Fast, efficient for large data")
    print("Weaknessnes: Key sharing is risky")

# Asymmetric Encryption
    if role == "admin":
        private_key = rsa.generate_private_key(public_exponent=65537, key_size=2048)
        public_key = private_key.public_key()
        message_rsa =b"Pumpkin Pie ice cream is the best!"
        print(f"message: {message_rsa.decode()}")
        encrypted_rsa = public_key.encrypt(
            message_rsa, 
            padding.OAEP(
                mgf=padding.MGF1(algorithm=hashes.SHA256()),
                algorithm=hashes.SHA256(),
                label=None
            )
        )
        print(f"Encrypted: {encrypted_rsa.hex()}")
        decrypted_rsa = private_key.decrypt(
            encrypted_rsa,
            padding.OAEP(
                mgf=padding.MGF1(algorithm=hashes.SHA256()),
                algorithm=hashes.SHA256(),
                label=None
            )
        )
        print(f"Decrypted Message: {decrypted_rsa.decode()}")
        print("\nStrengths: Secure key sharing, separate encrypt/decrypt keys")
        print("Weaknesses: Slower, limited message size")
        
    elif role == "user":
        print("\nAccess denied for asymmetric encryption")

    

if __name__ == "__main__":
    main()
