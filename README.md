# Serial Escape

To start with you can run a simple NMAP scan against the target.
```
nmap -sV -v <target IP>
```
![image](./images/2024-09-25_17h30_17.png)

Based on the NMAP results, there are two open ports: SSH (22) and HTTP (80).
If you navigate to the target IP address on a browser you will see a page as follows.

![image](./images/2024-09-25_17h30_48.png)

Based on the error message, you can understand that the base URL "/" requires authentication to visit.

To discover directories on the web application, you can use a directory brute-forcing tool like FFUF. This revealed an additional directory named 'dev', which also requires authentication.

![image](./images/2024-09-25_17h29_25.png)

When you navigate to the registration page to create a new user, you will see that the form requires you to enter an email address ending with "@oracle.az" only.

![image](./images/2024-09-25_17h31_16.png)

Upon registering, the application sends an OTP code to the provided email for verification, ensuring that users cannot create accounts with fake email addresses. However, after further inspection, you can notice that the email validation (checking if it ends with '@oracle.az') is only performed on the client side.

![image](./images/2024-09-25_18h56_23.png)

So, you can fill out the registration form with dummy inputs, capture the network traffic with Burp Suite, and replace the email address with one of your choice (either an email you own or a temporary/disposable email) to receive the OTP code and pass verification.
For demonstration purposes, a temporary email was used.
https://temp-mail.org/

![image](./images/2024-09-25_17h36_49.png)

Once you have placed a valid email address you can forward the traffic, which will send an OTP to the provided address.

![image](./images/2024-09-25_17h38_46.png)

To complete the registration process, enter the OTP code and submit.

![image](./images/2024-09-25_17h39_16.png)
![image](./images/2024-09-25_17h39_31.png)

As a result, you will be redirected to the login page with a success message indicating that you have successfully created a new user.
Having a user on the web application you can log in now. On the home page, you will see the first flag for the CTF.

![image](./images/2024-09-25_17h39_50.png)

Since you now have a valid user, you can visit the "dev" directory discovered earlier while directory brute-forcing with FFUF.
On the "dev" directory you will see two files: notes.txt and source.zip

![image](./images/2024-09-25_17h41_34.png)

The notes.txt file contains a message titled 'Security Alert,' highlighting a critical vulnerability in the application originating from a package called ```node-serialize```.
According to this message, you can understand that your next step will be downloading the source code of the web app (```/dev/source.zip```) and analyzing it to move the attack further. 

![image](./images/2024-09-25_17h41_42.png)

To begin your code review, start with the ```app.js``` file. In the ```app.js``` file, you'll also notice a warning comment regarding the ```node-serialize``` package.

![image](./images/2024-09-25_17h43_07.png)

To identify what the node-serialize package is vulnerable to, you can search online. You'll discover that it has a critical vulnerability: arbitrary remote code execution, which is explained in the following link. This vulnerability specifically affects the ```unserialize``` function, according to the explanation.
https://security.snyk.io/vuln/npm:node-serialize:20170208

Knowing that the web application is vulnerable to remote code execution, you can examine the source code further to find out where and how the ```node-serialize``` package is used. By searching in the ```app.js``` file, you'll find that the vulnerable package is passed to the ```home``` router after being imported.

![image](./images/2024-09-25_17h45_54.png)

To understand how and for what functionality of the web application the ```node-serialize``` package is used, open the JavaScript file responsible for the home page.
Upon reviewing the code, two key points emerge:
 [*] When a user searches for a keyword, it is taken from the request, serialized, and stored in a cookie named ```last_search```.
 [*] During each GET request to the home page, the value of the ```last_search``` cookie is retrieved, <u>unserialized</u> (which is the vulnerable part), and passed to the client side for display.
This functionality allows users to see their most recent search by storing its value in a cookie.

![image](./images/2024-09-25_17h54_27.png)

In the image below, you can see example usage of this functionality.

![image](./images/2024-09-25_18h12_04.png)
![image](./images/2024-09-25_18h22_19.png)
![image](./images/2024-09-25_18h24_52.png)
![image](./images/2024-09-25_18h26_10.png)
![image](./images/2024-09-25_18h30_58.png)
![image](./images/2024-09-25_18h33_34.png)
![image](./images/2024-09-25_18h34_12.png)
![image](./images/2024-09-25_18h35_11.png)
