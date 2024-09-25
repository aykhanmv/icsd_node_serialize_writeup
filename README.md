# Serial Escape

To start with you can run a simple NMAP scan against the target.
```
nmap -sV -v <target IP>
```
![image](./images/2024-09-25_17h30_17.png)

Based on the NMAP results, there are two ports open: SSH (22) and HTTP (80).
If you navigate to the target IP address on a browser you will see a page as follows.

![image](./images/2024-09-25_17h30_48.png)

Based on the error message, you can understand that the base URL "/" requires authentication to visit.

To discover directories on the web application, you can use a directory brute-forcing tool like FFUF. This revealed an additional directory named 'dev', which also requires authentication.

![image](./images/2024-09-25_17h29_25.png)

When you navigate to the registration page, you will see that the form requires you to enter an email address ending with "@oracle.az" only.

![image](./images/2024-09-25_17h31_16.png)

Upon registering, the application sends an OTP code to the provided email for verification, ensuring that users cannot create accounts with fake email addresses. However, after further inspection, you can notice that the email validation (checking if it ends with '@oracle.az') is only performed on the client side.

![image](./images/2024-09-25_18h56_23.png)

Som, you can fill out the registration form with dummy inputs, capture the network traffic with Burp Suite, and replace the email address with one of your choice (either an email you own or a temporary/disposable email) to receive the OTP code and pass verification.

For demonstration purposes, a temporary email was used.
https://temp-mail.org/



![image](./images/2024-09-25_17h32_38.png)
![image](./images/2024-09-25_17h33_19.png)
![image](./images/2024-09-25_17h33_58.png)
![image](./images/2024-09-25_17h35_40.png)
![image](./images/2024-09-25_17h36_49.png)
![image](./images/2024-09-25_17h37_15.png)
![image](./images/2024-09-25_17h37_36.png)
![image](./images/2024-09-25_17h38_24.png)
![image](./images/2024-09-25_17h38_46.png)
![image](./images/2024-09-25_17h39_16.png)
![image](./images/2024-09-25_17h39_31.png)
![image](./images/2024-09-25_17h39_50.png)
![image](./images/2024-09-25_17h41_34.png)
![image](./images/2024-09-25_17h41_42.png)
![image](./images/2024-09-25_17h42_59.png)
![image](./images/2024-09-25_17h43_07.png)
![image](./images/2024-09-25_17h45_54.png)
![image](./images/2024-09-25_17h47_51.png)
![image](./images/2024-09-25_17h54_27.png)
![image](./images/2024-09-25_18h08_36.png)
![image](./images/2024-09-25_18h12_04.png)
![image](./images/2024-09-25_18h22_19.png)
![image](./images/2024-09-25_18h24_52.png)
![image](./images/2024-09-25_18h26_10.png)
![image](./images/2024-09-25_18h30_58.png)
![image](./images/2024-09-25_18h33_34.png)
![image](./images/2024-09-25_18h34_12.png)
![image](./images/2024-09-25_18h35_11.png)
