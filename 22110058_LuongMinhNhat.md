# Lab #2,22110058, Luong Minh Nhat, INSE330380E_01FIE

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

![image](https://github.com/user-attachments/assets/c12eff10-e8b5-4b23-9867-ae53c5642cd1)


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

Step 1: Generate RSA Keys on Computer B (Receiver)
1. Generate a 2048-bit RSA private key: <br>
```
openssl genpkey -algorithm RSA -out private.key -pkeyopt rsa_keygen_bits:2048
```
<br> 
2. Extract the corresponding public key: <br>

```
openssl rsa -pubout -in private.key -out public.key
```
<br>

3. Transfer the public.key to Computer A (Sender): <br>

```
scp public.key nhat317@172.29.218.4:/home/nhat317
```
<br>

Step 2: Encrypt the Symmetric Key on Computer A (Sender)
1. Generate a random symmetric key for AES encryption: <br>

```
openssl rand -base64 32 > symmetric.key
```

<br>
2. Encrypt the symmetric key using Computer B's public.key: <br>

```
openssl rsautl -encrypt -inkey public.key -pubin -in symmetric.key -out symmetric.key.enc
```
Step 3: Encrypt the File on Computer A (Sender)
1. Create the file to be encrypted (e.g., message.txt): <br>

```
echo "This is a secure file transfer example!" > message.txt
```
2. Encrypt the file using the symmetric key: <br>
```
openssl enc -aes-256-cbc -salt -in message.txt -out message.txt.enc -pass file:./symmetric.key
```
3. Transfer the encrypted file (message.txt.enc) and the encrypted symmetric key (symmetric.key.enc) to Computer B: <br>
```
scp message.txt.enc symmetric.key.enc nhat317@172.29.218.4:/home/nhat317
```
<br>
Step 4: Decrypt the Symmetric Key on Computer B (Receiver)
1. Decrypt the symmetric key using the private RSA key <br>

```
openssl rsautl -decrypt -inkey private.key -in symmetric.key.enc -out symmetric.key
```

Step 5: Decrypt the File on Computer B (Receiver)
1. Decrypt the file using the symmetric key: <br>

```
openssl enc -d -aes-256-cbc -in message.txt.enc -out message.txt -pass file:./symmetric.key
```

2. Verify the content of the decrypted file: <br>
```
cat message.txt
```
<br>

# Task 3: Firewall configuration

**Question 1**:
From VMs of previous tasks, install iptables and configure one of the 2 VMs as a web and ssh server. Demonstrate your ability to block/unblock http, icmp, ssh requests from the other host.

**Answer 1**:
We have 2 machines:
![image](https://github.com/user-attachments/assets/c12eff10-e8b5-4b23-9867-ae53c5642cd1)

![image](https://github.com/user-attachments/assets/ea7f5a3a-f88a-4062-a4f5-1ec76f474b7d)


Configure iptables Rules on Machine 10.0.2.15
1. Allow All Traffic Initially: <br>
```
sudo iptables -F           
sudo iptables -P INPUT ACCEPT
sudo iptables -P FORWARD ACCEPT
sudo iptables -P OUTPUT ACCEPT

```
2. Block HTTP Traffic (Port 80): <br>
```
sudo iptables -A INPUT -p tcp --dport 80 -s 172.29.218.4 -j DROP
```
3. Unblock HTTP Traffic: <br>
```
sudo iptables -D INPUT -p tcp --dport 80 -s 172.29.218.4 -j DROP

```
4. Block ICMP Traffic (Ping): <br>
```
sudo iptables -A INPUT -p icmp -s 172.29.218.4 -j DROP

```
5. Unblock ICMP Traffic: <br>
```
sudo iptables -D INPUT -p icmp -s 172.29.218.4 -j DROP
```
6. Block SSH Traffic (Port 22): <br>
```
sudo iptables -A INPUT -p tcp --dport 22 -s 172.29.218.4 -j DROP
```
7. Unblock SSH Traffic: <br>
```
sudo iptables -A INPUT -p tcp --dport 22 -s 172.29.218.4 -j DROP
```


