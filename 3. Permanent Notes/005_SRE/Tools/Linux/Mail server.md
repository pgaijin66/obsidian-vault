Tags: #smtp #mail 

You can get some of the SMTP mailbox details from the command line using the "nslookup" and 

Use the "nslookup" command followed by the email provider's domain name to retrieve the MX (mail exchange) records. For example, to retrieve the MX records for Gmail, type:
  
  bashCopy code

 `nslookup -type=mx gmail.com`

The MX records will be displayed, and you'll want to note the hostname of the mail server with the lowest preference number.
    
 Use the "telnet" command to connect to the mail server on port 25 (SMTP). For example, to connect to the Gmail mail server, type:

Copy code

`telnet gmail-smtp-in.l.google.com 25`

Once connected, you can use the SMTP commands to retrieve some of the details. For example, to retrieve the server name and port numbers, type:
  
 `EHLO example.com`


This will display the server's name and the port numbers used for SMTP communication.

To exit the telnet session, type "QUIT" and press Enter.