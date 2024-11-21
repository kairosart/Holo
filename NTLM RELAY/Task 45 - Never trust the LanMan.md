You now have administrator access to PC-FILESRV01; you know that you are in a domain content. Your research team has developed a brand new never-seen exploit to relay requests and dump domain credentials. We will cover this new exploit in the following four tasks and weaponize it to gain domain administrator access and own the domain with a relay.  

To begin this attack, we will identify what NTLM (New Technology LAN Manager) is and how it is integrated into Windows.

Net-NTLMv1 is a challenge/response protocol that uses NTHash. This version will use both NT and LM hashes. You can find the algorithm used to hash below.

`C = 8-byte server challenge, random K1 | K2 | K3 = LM/NT-hash | 5-bytes-0 response = DES(K1,C) | DES(K2,C) | DES(K3,C)`  

Net-NTLMv2 is an updated version of Net-NTLMv1. This hash protocol will use the same processes as v1 but will use a different algorithm and response. This version is the default since Windows 2000.  

`SC = 8-byte server challenge, random CC = 8-byte client challenge, random CC* = (X, time, CC2, domain name) v2-Hash = HMAC-MD5(NT-Hash, user name, domain name) LMv2 = HMAC-MD5(v2-Hash, SC, CC) NTv2 = HMAC-MD5(v2-Hash, SC, CC*) response = LMv2 | CC | NTv2 | CC*`

---
Now that we understand what an NTLM hash is and how it is hashed, we can look at how it responds and requests and why it can only be relayed and not replayed.  

The reason we can only relay hashes is that it uses a **challenge-based request**. A client will attempt to authenticate to a server; the server will approve or deny initial authentication and move on to send a client a Challenge string to encrypt with the client's NTLM hash (Challenge-Request). If the client can encrypt the string correctly, the client will be permitted to authenticate to the server; if not, authentication will fail (Challenge-Response). We can break down the technical process below.

1. (Interactive authentication only) A user accesses a client computer and provides a domain name, user name, and password. The client computes a cryptographic _[hash](https://docs.microsoft.com/en-us/windows/win32/secgloss/h-gly)_ of the password and discards the actual password.
2. The client sends the user name to the server (in _[plaintext](https://docs.microsoft.com/en-us/windows/win32/secgloss/p-gly)_).
3. The server generates a 16-byte random number, called a _challenge_ or _[nonce](https://docs.microsoft.com/en-us/windows/win32/secgloss/n-gly)_, and sends it to the client.
4. The client encrypts this challenge with the hash of the user's password and returns the result to the server. This is called the _response_.
5. The server sends the following three items to the domain controller:
    - User name
    - Challenge sent to the client.
    - Response received from the client.
6. The domain controller uses the user name to retrieve the hash of the user's password from the Security Account Manager database. It uses this password hash to encrypt the challenge.
7. The domain controller compares the encrypted challenge it computed (in step 6) to the response computed by the client (in step 4). If they are identical, authentication is successful.

Source: [https://docs.microsoft.com/en-us/windows/win32/secauthn/microsoft-ntlm?redirectedfrom=MSDN](https://docs.microsoft.com/en-us/windows/win32/secauthn/microsoft-ntlm?redirectedfrom=MSDN)

Now that we understand Net-NTLMv1 and Net-NTLMv2 and how they can be used for authentication, we can move on to exploiting Net-NTLM.

**Next step:** [[Task 46 - Now you see me, now you don't]]
