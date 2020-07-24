# Generate-ssh-keygen.md

`ssh-keygen -t rsa -b 4096 -C "abc@gmail.com"`

- `ssh-keygen` generate SSH key paris
- `rsa` algorithm
- `t` type
- `b` bit
- `C` comment, label

## Checking ssh-agent running

- Mac and Linux

  `eval "$(ssh-agent -s)"`

- Windows

  `eval $(ssh-agent -s)`

## Adding ssh key

`ssh-add -K ~/.ssh/id_rsa`

\*id_rsa is private key filename

## Testing ssh connection with github server

`ssh -T git@github.com`

### Similar Doc Ref:

https://docs.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent
