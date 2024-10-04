# node-server-setup-guide
Steps I always have to go through when setting up a new NodeJS/PostgreSQL back-end.


## Table of Contents ##
* [Install Node](#install-node)
* [Setup git](#setup-git)
* [Unlocking .env file](#encrypt)
* [Database/Postgresql](#database-psql)


## <a name="install-node"></a> Install Node ##
- Update Ubuntu: `sudo apt-get update`.
- Install node: `sudo apt install nodejs`. Verify it worked with: `node -v`.
- Install npm (node-package-manager): `sudo apt install npm`. Verify with `npm -v`.
- Install n (version manager for node): `sudo npm i -g n`.
- Use 'n' to install the latest lts (long-term-support) version of node: `sudo n lts`.
- Restart the shell to switch to the latest version of node.
- Install ts-node to run development files: `sudo npm i -g ts-node`.
- Install typescript globally to build development files (with the tsc command): `sudo npm install -g typescript`.


## <a name="setup-git"></a> Setup git ##
- Create a github account if you don't have one. 
- Install/setup git:
    - `sudo apt install git`
    - `git config --global color.ui true`
    - `git config --global user.name "YOUR NAME"`
    - `git config --global user.email "YOUR@EMAIL.com"`
    - `ssh-keygen -t rsa -b 4096 -C "YOUR@EMAIL.com"` (just hit enter at all the prompts)
    - `cat ~/.ssh/id_rsa.pub` (copy the output)
    - Go to `https://github.com/settings/keys`
    - Click on the "New SSH key" button in the top right
    - Name it whatever you want.
    - Paste the output from the `cat` command into the key text area.
    - Run `ssh -T git@github.com`. Type yes at the prompt.
    - If if worked you should see:
        - Hi excid3! You've successfully authenticated, but GitHub does not provide shell access.
    - Clone the repo: `git clone git@github.com:seanpmaxwell/repo_name_here`


## <a name="encrypt"></a> Encrypting .env files ##
- Run a git commit if you've made any changes to the repo. Must have clean working directory to run unlock.
- Install git-crypt: `sudo apt-get install -y git-crypt`.
- In the terminal, go into the project directory.
- To encrypt a file
- - Make sure the file is specified under .gitattributes
- - run `git-crypt init`
- To decrypt a file
- - Get the gitcrypt key `dev` from someone. ONLY developers which handle deployment should have `prod`. 
- - Place it under `.git/git-crypt/keys/`. (may need to manually create the the parent dirs if they're not there)
- - git-crypt unlock: `git-crypt unlock .git/git-crypt/keys/key_name_here`.
- - To encrypt the files (maybe you need to test if your .gitattributes is working): `git-crypt lock`


### <a name="database-psql"></a> Setup database (Ubuntu)
- Install postgresq.
 - `sudo apt update`
 - `sudo apt install postgresql postgresql-contrib`
 - `sudo service postgresql start`
- To use postgres, we need a role and a database that matches the roles name:
 - `sudo -u postgres createuser --interactive`
  - If you get this error `could not change directory to "/home/user_name": Permission denied` then you maybe need to run `sudo chmod og+rX /home /home/user_name`
  - Enter your linux user name:
  - Enter 'y' for Shall the new role be a superuser?
- Create a new database for the superuser: 
 - `sudo -u postgres createdb "linux user name"`
- Create a password the new pg user: 
 - `sudo -u "linux user name" psql`
 - `\password`
 - Enter a password. <b>DO NOT</b> forget it.
 - `\q`
