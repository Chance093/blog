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

1. Generate a key

> ```bash
> ssh-keygen -t ed25519 -C "your_email@example.com"
> ```
> 
> Make sure you change the email inside the quotes to an email of yours.
>
> This will generate a key using the ed25519 encryption algorithm, and uses the 
> provided email as a label so you can easily identify it. You can change the 
> algorithm you would like to use to encrypt your key, but GitHub recommends 
> using this one. 

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
> Using a passphrase is an extra layer of protection for your SSH keys. If 
> someone ever gets access to your private key, they have access to your stuff. 
> The passphrase prevents this from happening!
> 
> If you decide to use a passphrase, you will always be asked what that 
> passphrase is everytime you do a pull or push to your remote repository. There 
> is a way to avoid this by using keychains. If you would like to know more 
> about this, here is a link to get more info: [Ubuntu / Debian Linux Install 
> Keychain SSH Key Manager For OpenSSH](https://www.cyberciti.biz/faq/ubuntu-debian-linux-server-install-keychain-apt-get-command/)
> 
> *Note: I have seen this also solved by using ssh-agent to add all of your 
> keys. They put this in their .bashrc to automate it when you first boot up 
> your machine, but I have never done it myself so I can't speak on it.*

## Setting Up Your SSH Config

This is the most important part when it comes to managing multiple keys. Your 
ssh config will tell your ssh client where it should be looking for your ssh 
keys, and which key it should be using.

If you have multiple ssh keys, or if you have a single key and changed the name 
or location of that key, you will need a ssh config to fix some issues you will 
have.

1. Create an ssh config file

> Move to your ssh directory and create a config file
> ```bash
> cd ~/.ssh && touch config
> ```

2. Open config and copy the example:

> ```conf
> Host github.com
>   Hostname github.com
>   User git
>   IdentityFile ~/.ssh/[your_personal_ssh_key]
>   IdentitiesOnly yes
>  
> Host work.github.com
>   Hostname github.com
>   User git
>   IdentityFile ~/.ssh/[your_work_ssh_key_file]
>   IdentitiesOnly yes
> 
> Host work.gitlab.com
>   Hostname gitlab.com
>   User git
>   IdentityFile ~/.ssh/[your_work_gitlab_ssh_key_file]
>   IdentitiesOnly yes
>```
>
> Make sure you change up the file to work for you. Host can be anything you 
> want (it should easily identify what host you are using and what work you are 
> doing). Hostname should always be the website you are connecting to. Identity 
> file should point to the ssh key you are going to use. 
> 
> In the `Cloning a Repo` Section, it should make sense how we can connect 
> different repos to different keys. It is very important to realize that this 
> ssh config file is telling your ssh client which key it should be looking for 
> every time its setting up a connection from a certain host.

## Connecting Your SSH Keys

You will need to add your public key to the host you are trying to connect to 
(Github/GitLab). This allows the host to connect with your machine through ssh 
in a very secure way. Connecting your ssh key is very simple.

### GitHub SSH Connection

1. Copy the SSH key to your clipboard

> You will need to copy your public key (NOT YOUR PRIVATE KEY!). Copy the 
> example code, and make sure to point to the correct public key.
> 
> **Linux**
> ```bash
> xclip -sel clip < ~/.ssh/id_ed25519.pub
> ```
> 
> **Git Bash on Windows**
> ```bash
> cat ~/.ssh/id_ed25519.pub | clip
> ```

2. In the upper-right corner of any page on GitHub, click your profile photo, 
then click **Settings**.

3. In the "Access" section of the sidebar, click **SSH and GPG keys**.

4. Click **New SSH key** or **Add SSH key**.

5. In the **Title** field, add a descriptive label for the new key. For example, 
if you're using a home desktop, you could call this key "Home Desktop".

6. Select the type of key, either authentication or signing. I always just 
choose authentication which is the default.

7. In the **Key** field, paste your public key.

8. Click **Add SSH key**.

9. If prompted, confirm access to your account on GitHub.

### GitLab SSH Connection

1. Copy the SSH key to your clipboard

> You will need to copy your public key (NOT YOUR PRIVATE KEY!). Copy the 
> example code, and make sure to point to the correct public key.
> 
> **Linux**
> ```bash
> xclip -sel clip < ~/.ssh/id_ed25519.pub
> ```
> 
> **Git Bash on Windows**
> ```bash
> cat ~/.ssh/id_ed25519.pub | clip
> ```

2. Sign in to GitLab.

3. On the left sidebar, select your avatar.

4. Select **Edit profile**.

5. On the left sidebar, select **SSH Keys**.

6. Select **Add new key**.

7. In the **Key** field, paste your public key.

8. In the **Title** field, add a descriptive label for the new key. For example, 
if you're using a home desktop, you could call this key "Home Desktop".

9. Optional. Select the **Usage type** of the key. It can be used either for 
Authentication or Signing or both. Authentication & Signing is the default 
value.

10. Optional. Update **Expiration date** to modify the default expiration date.

11. Select **Add key**.

### Test Your SSH Connection

You should test your ssh connection before trying to clone your repository. 
Here are some links that tell you how you can test your ssh connection:

- [Test your SSH connection with GitHub](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/testing-your-ssh-connection)
- [Test your SSH connection with GitLab](https://docs.gitlab.com/ee/user/ssh.html#verify-that-you-can-connect)

## Clone Your Repo

This is where our config will start to make sense. When you usually clone your 
repo to your machine locally, you can just copy the ssh URL. Well now we have 
to change up the URL just a bit. 

Below are examples of each part of the config, and how you would clone a repo 
for each ssh key:

2. Personal ssh key (GitHub)

> Config:
> ```conf
> Host github.com
>   Hostname github.com
>   User git
>   IdentityFile ~/.ssh/[your_ssh_key_file]
>   IdentitiesOnly yes
> ```
>
> Git clone:
> ```bash
> git clone git@work.github.com:work-profile/repo.git
> ```

2. Work ssh key (GitHub)

> Config: 
> ```conf
> Host work.github.com
>   Hostname github.com
>   User git
>   IdentityFile ~/.ssh/[your_work_ssh_key_file]
>   IdentitiesOnly yes
> ```
>
> Git clone:
> ```bash
> git clone git@work.github.com:work-profile/repo.git
> ```

3. Work ssh key (GitLab)

> Config: 
> ```conf
> Host work.gitlab.com
>   Hostname gitlab.com
>   User git
>   IdentityFile ~/.ssh/[your_work_gitlab_ssh_key_file]
>   IdentitiesOnly yes
>```
> 
> Git clone:
> ```bash
> git clone git@work.gitlab.com:work-profile/repo.git
> ```

## Summary

It is actually suprisingly easy to get this all set up. You create your ssh 
key, set up an ssh config, connect your public key to the host, and clone your 
repo. If there are any concepts I didn't really touch up on, there are tons of 
great resources that go much more in depth than I did. The intention of this 
was to get you a quick way to get your ssh keys set up in a simple way.
