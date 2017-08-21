# Using the MSF hashing on your PHP site

Master Server Framework uses the PBKDF2 algorithm for hashing passwords.
So if you want to integrate MSF with your application you'll need to use the same hashing type for passwords.

Lucky we can use the [defuse PBKDF2 implementation for PHP](https://github.com/defuse/password-hashing)!

## Downloading and modifying the constants

* First download the PasswordStorage.php file from the author github page.
* Modify the constants according to MSF settings following the example below:

``` 
    const PBKDF2_HASH_ALGORITHM = "sha1";
    const PBKDF2_ITERATIONS = 1000;
    const PBKDF2_SALT_BYTES = 24;
    const PBKDF2_OUTPUT_BYTES = 24;
```

## Use example
```
<?php
    include_once("path/to/PasswordStorage.php");
    $hashingHelper = new PasswordStorage;
    $encodedString = $hashingHelper->create_hash($_POST["password"]); //Here we're using a POST form, encode whatever you need
``` 