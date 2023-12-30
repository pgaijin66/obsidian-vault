
## For unauthenticated user ( password reset or forgot password)

[[API]] endpoint
```
PUT /v1/account/password 202 Accepted

Body
{
email: 
old_password:
new_password:
}

```

Opening the link from this email will direct to a reset password form on the front end application that uses the reset password token from the link as input for a hidden input field (the token is part of the link as a query string). Another input field allows the user to set a new password. A second input to confirm the new password will be used for validation on front-end (to prevent typos).


Once request is sent user will receive an email at provided email and provided password reset email link

```
https://example.com/password-reset?token=1234567890
```



## For authenticated user 

Re-authenticate user

```
PUT : /v1/account/password
```

Request body

```
{
    "old":"password",
    "new":"password"
}
```


#### Keywords

#password-reset #api #REST