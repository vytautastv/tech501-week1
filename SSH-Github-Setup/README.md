# SSH keys and connecting to github

## **1️⃣ Generate SSH Key**
First, navigate to the `.ssh` directory:
```bash
cd ~/.ssh

generate a new keypair:
ssh-keygen -t rsa -b 4096 -C "vytautastv98@gmail.com"

call the file name: vytautas-github-key

use ls in the ssh folder to see both private and public keys

show the public key in terminal using cat:
cat ~/.ssh/vytautas-github-key.pub

Add the key to github:
	1.	Go to GitHub SSH Settings.
	2.	Click “New SSH Key”.
	3.	Title: vytautas-github-key
	4.	Paste the copied key in the text box.
	5.	Click “Add SSH Key”.


Start SSH Agent:
eval `ssh-agent -s`

Add the private key:
ssh-add ~/.ssh/vytautas-github-key

Test the connection:
ssh -T git@github.com - should see Hi vytautastv! You've successfully authenticated, but GitHub does not provide shell access.


Create a repo: c
 ~/Documents/Github
mkdir tech501-test-ssh
cd tech501-test-ssh

initialise git tracking:
git init

Echo a readme file:
echo "# Tech-501-test-ssh" > README.md
cat README.md

Set branch to main:
git branch -M main

Add remote repo:
git remote add origin git@github.com:vytautastv/tech501-vytautas-test-ssh.git

stage and commit changes:
git add .
git commit -m "Initial commit with README"

push changes to github:
git push -u origin main
