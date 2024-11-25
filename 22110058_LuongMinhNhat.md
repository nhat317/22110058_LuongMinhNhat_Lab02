# Lab #1,22110058, Luong Minh Nhat, INSE331280E_02FIE

# Task 1: Transfer files between computers

**Question 1**:
Conduct transfering a single plaintext file between 2 computers,
Using openssl to implementing measures manually to ensure file integerity and authenticity at sending side,
then veryfing at receiving side.

**Answer 1**:
_First, we write a message and save it in a text file:_<br>

```sh
echo "Sir, when will we meet again?" > plain.txt
```

<br>
Verify current folder for newly created file
<br>

**On the Sending Side**

<br>
1. Create a cryptographic hash (checksum) of the file for integrity: Generate a SHA-256 checksum of the plaintext file (e.g., file.txt): <br>

```sh
openssl dgst -sha256 plain.txt > plain.txt.sha256
```

<br>
2. Sign the checksum for authenticity: Generate a private key (if you don't already have one):<br>

<br>

```sh
openssl genpkey -algorithm RSA -out private.key -pkeyopt rsa_keygen_bits:2048
```

Extract the corresponding public key to share with the receiver: <br>

```sh
 openssl rsa -pubout -in private.key -out public.key
```

<br>
Sign the checksum using the private key:
<br>

```sh
openssl dgst -sha256 -sign private.key -out plain.txt.sha256.sig plain.txt.sha256
```

<br>

![image](https://github.com/user-attachments/assets/da176379-3e7e-4f98-8589-01d01021c7e1)

<br>
3. Send the files to the receiver: Using scp
<br>
Find receiving machine's Ip:

![image](https://github.com/user-attachments/assets/511095a2-45a9-42ea-ae13-6eb9cc28f302)

<br>
Sending File:

![image](https://github.com/user-attachments/assets/a4580212-1a96-4406-940c-7b5ce06a4bd5)


<br>

**On the Receiving Side** 

<br>

Verify the authenticity of the checksum: Use the sender's public key to verify the signature of the checksum

<br>

```sh
openssl dgst -sha256 -verify public.key -signature plain.txt.sha256.sig plain.txt.sha256
```
If the output is:
<br>

```Verified OK```
<br>
This means the checksum file is authentic and hasn't been tampered with. If it doesn't verify, the process should stop

# Task 2: Transfering encrypted file and decrypt it with hybrid encryption.

**Question 1**:
Conduct transfering a file (deliberately choosen by you) between 2 computers.
The file is symmetrically encrypted/decrypted by exchanging secret key which is encrypted using RSA.
All steps are made manually with openssl at the terminal of each computer.

**Answer 1**:

# Task 3: Firewall configuration

**Question 1**:
From VMs of previous tasks, install iptables and configure one of the 2 VMs as a web and ssh server. Demonstrate your ability to block/unblock http, icmp, ssh requests from the other host.

**Answer 1**:

```

```
