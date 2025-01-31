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

