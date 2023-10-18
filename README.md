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
- Install git-crypt: `sudo apt-get install -y git-crypt`.
- Go into the project directory: `cd PhotonSuite`.
- `git-crypt init`
- Get the gitcrypt key `dev` from someone. ONLY developers which handle deployment should have `prod`. 
- Place it under `.git/git-crypt/keys/`.
- git-crypt unlock: `git-crypt unlock .git/git-crypt/keys/dev`.


## <a name="database-psql"></a> Database/Postgresql ##
- Install postgresql:
    - `sudo apt update`
    - `sudo apt install postgresql postgresql-contrib`
- Start postgres: `sudo service postgresql start`
- Create a postgres superuser for connecting locally. Postgres assumes that for database role,
there is a cooresponding database and linux role with the same name. So we need to create both a 
postgres user, database, and linux user.
    - Open up the file `development.env`
    - Notice the values for `PGUSER` and `PGPASSWORD`.
    - Run: `sudo -u postgres createuser --interactive`.
        - For name of the new role enter the value for `PGUSER`.
        - Enter 'y' for `Shall the new role be a superuser?`.
    - Create a new database for the superuser: `sudo -u postgres createdb "Value for PGUSER"`.
    - Create a linux role for the new user: `sudo adduser "Value for PGUSER"`
        - Enter any value you want for the password but don't forget it.
        - Just press enter for the remaining prompts.
    - Run: `sudo -u "Value for PGUSER" psql`
        - Type `\password`
        - Enter the value for `PGPASSWORD`.
        - Type `\q` to return to the normal command prompt.
- Following commands must be done from `db/` folder.
- Setup db. This has to be done be done before connecting. Option for environment file `--env` 
    (i.e. development):
    - Run `npm i` to install dependencies if you haven't done so yet.
    - Example: `npm run setup -- --env=development`.
    - This has to be done for remote databases too. Setup script accounts for this with the 
    `LOCAL` environment variable.
- Handle Migrations (default envivornment is `development`):
    - Create new. Name it based on what the migration does and don't use spaces:
        - `npm run mig-create -- --migName="Migration Name"`
    - Run migrations. This will run all migrations not yet run: 
        - `npm run mig-run -- --env="Environment File"`.
    - Rollback migration:
        - `npm run mig-rb -- --env="Environment File"`.
- Inserting dummy-data:
    - Create a `.ts` file under: `db/dummyData/`
    - Make sure to `export default` the root function.
    - Run the file with: `npm run dummy-data -- --script="name of the file"`
    - You can still use the `--env` option to specify the environment (default `development`).
    - Example to insert users: `npm run dummy-data -- --script=users.ts`
