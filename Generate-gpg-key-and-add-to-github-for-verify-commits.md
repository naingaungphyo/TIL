# Generate gpg key and add to Github for verify commits

## Generating gpg key
  * Download and install GPG Suite from https://gpgtools.org/
  * Generate gpg key from terminal or from GPG Keychain App UI
    - To generate from terminal, use the following command. 
      - must use RSA (choose default for RSA)
      - key must be at least 4096 bits
      - use GitHub-provided no-reply email to keep your private email  
    #### ```gpg --full-generate-key```  
  * List your generated key  
    #### ```gpg --list-secret-keys --keyid-format LONG```
  * Get the public key to add to github
    #### ```gpg --armor --export yourGPGKeyIdHere```. (eg GPG Key ID:  ```3AA5C34371567BD2```)
  * Copy your GPG key and add it in below github setting, beginning with -----BEGIN PGP PUBLIC KEY BLOCK----- and ending with -----END PGP PUBLIC KEY BLOCK-----.
    #### ```https://github.com/settings/keys```
## Edit git config to make sign commit
  * Set the same GitHub-provided no-reply email or private email that you use when generating gpg key to git config.
    #### ```git config --global user.email youremail@users.noreply.github.com```
  * Set the GPG Key to git config. (Note: this is global setting example, you can use for local setting too)
    #### ```git config --global user.signingkey yourGPGKeyIdHere``` (eg GPG Key ID:  ```3AA5C34371567BD2```)
  * Make sign commit using -S option or make every commit as sign commit by configuring git config
    #### ```git commit -S -m "your commit message"```  
     (or)
    #### ```git config --global commit.gpgsign true```

## Command Ref: 
- To check git config list, use ```git config --list```
- To remove git config variable, use ```git config --global --unset "variable-name-here"```
- To check generated gpg keys list, use ```gpg --list-secret-keys --keyid-format LONG```

## Doc Ref:
https://docs.github.com/en/github/authenticating-to-github/generating-a-new-gpg-key#generating-a-gpg-key
https://docs.github.com/en/github/authenticating-to-github/telling-git-about-your-signing-key
https://docs.github.com/en/github/authenticating-to-github/signing-commits

