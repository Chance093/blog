# Managing Multiple SSH Keys for Github and Gitlab

## Motivation

Up until now, I have not needed to manage multiple SSH keys on a single machine. 
I have always used one SSH key for my personal github account, which my job has
allowed me to use when working on the company repo. Recently, we had to move 
the repo over to GitLab. Also, we started another project on GitHub which now 
can only be accessed by my company account. So now I have my personal GitHub 
account, work GitHub account, and work GitLab account.

I decided to look into how SSH keys work, how we can connect them to GitHub and 
GitLab, and how we can manage multiple without having repo permissions clash 
with each other. It wasn't easy to get all my answers, and I needed to look into 
a lot of different sources. So I decided to share what I learned about how all 
of this works.

*Note: The environment I am working in is Windows with WSL. Everything I have 
listed here should work with Linux, and Windows if you either have Git Bash or 
WSL. As for Mac, some of the sources I link should point you in the right 
direction.*

## Creating SSH Keys

First thing we need to know is how we can create SSH keys that we can use to 
link to our GitHub/GitLab. There is a very easy way to do this in our terminals.

1. Paste the text in your terminal, but change the email in the quotes to yours.

> ```bash
> ssh-keygen -t ed25519 -C "your_email@example.com"
> ```
> 
> This will generate a key using the ed25519 encryption algorithm, and uses the 
> provided email as a label so you can easily identify it. You can change the 
> algorithm you would like to use to encrypt your key, but GitHub recommends using 
> this one. 

2. Enter the file in which you would like to store your key

> ```shell
> > Enter a file in which to save the key (/home/YOU/.ssh/id_ALGORITHM):[Press enter]
> ```
> 
> Pressing enter will save the file in the default location: `~/.ssh/id_ed25519`.
> 
> I recommend naming the file something easily identifiable like `id_ed25519_work_github`,
> especially if you plan on having multiple SSH keys on one machine.

3. Enter a passphrase (Optional)

> ```shell
> > Enter passphrase (empty for no passphrase): [Type a passphrase]
> > Enter same passphrase again: [Type passphrase again]
> ```
> 
> Using a passphrase is an extra layer of protection for your SSH keys. If someone
> ever gets access to your private key, they have access to your stuff. The 
> passphrase prevents this from happening!
> 
> If you decide to use a passphrase, you will always be asked what that passphrase
> is everytime you do a pull or push to your remote repository. There is a way to 
> avoid this by using keychains. If you would like to know more about this, here is 
> a link to get more info: [Ubuntu / Debian Linux Install Keychain SSH Key Manager 
> For OpenSSH](https://www.cyberciti.biz/faq/ubuntu-debian-linux-server-install-keychain-apt-get-command/)
> 
> *Note: I have seen this also solved by using ssh-agent to add all of your keys. 
> They put this in their .bashrc to automate it when you first boot up your machine, 
> but I have never done it myself so I can't speak on it.*


