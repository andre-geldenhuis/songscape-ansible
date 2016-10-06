# Songscape deployent via Ansible WIP

## Setup ansible

Clone this repository to a handy directory say ~/projects/
```bash
git clone https://github.com/andre-geldenhuis/songscape-ansible
cd songscape-ansible
```

Create a python virtualenv so you can run the latest version of ansible (or follow your
usual process if you use ansible regularly) and start your python virtual enviroment.
```bash
virtualenv venv
source venv/bin/activate
```
Now install ansible with pip
```bash
pip install ansible
```
Everytime you close your terminal  and want to come back and use ansible again - navigate to
your songscape-ansible directory and run
```bash
source venv/bin/activate
```

## Setup users - you can ignore this as it should be done already
This should be done on the dev and prod servers already. Don't worry about it unless you are running on a new server.

In sites.yml make sure that all the roles except user_config is commented out.  There are
better ways to do this, we can seperate out the plays, but this will do for now.

Run the site.yml play but specify either the root user on the new server or a user with
sudo rights.  I'll use the example of a server with the first user being andre and using a
password.  This limits to just the dev server, specify --limit prod for production.

```
ansible-playbook --ask-pass --ask-sudo-pass -u andre -i hosts site.yml --limit dev
```

After running this play you'll not need to use a password (though it will still exist, so make sure it is good!  TODO fix this) and can run plays without the --ask-pass --ask-sudo-pass flags

## Mount Storage - you can ignore this as it should be done already
This should be done of the prod server already.

In sites.yml make sure that all the roles except research_mount is commented out.  There are better ways to do this, we can seperate out the plays, but this will do for now.

To run the deployemnt against the dev server (replace my username with yours):
```
ansible-playbook -u andre -i hosts site.yml --limit dev
```


## Install songscape
This is partially done on the dev and prod servers.

Make sure you have copied your ssh key to keys/ (or use your normal process).  Run chmod 600 on the key (you only need to do this once).

In sites.yml make sure that all the roles except songscape is commented out.  There are better ways to do this, we can seperate out the plays, but this will do for now.

To run the deployemnt against the dev server (replace my username with yours):
```bash
ansible-playbook -u andre -i hosts site.yml --limit dev
```

This will also create several new files in the root of your songscape-ansible directory:
secret_key_app and secret_key_db these contain the app secret key and the database password.
Note that these are generated when you first run the deployment and it can't see these files.  As such between different devs it generate different database passwords and app secret_keys.  This will cause problems particularly with the database password.  We can handle this more elegantly in future by encryping the keys and sharing them on github.  For now we can probably just share the database password and app_secret_key via other channels.

## Accessing the servers

Make sure you have copied your ssh key to keys/ (or use your normal process).  Run chmod 600 on the key.

From your songscape-ansible directory run (replace my username with yours)
```bash
ssh -i keys/andre andre@52.62.53.204 #to access the dev server
ssh -i keys/andre andre@vuwunicohalo001.vuw.ac.nz # to access the prod server (you will need to VPN to the uni network first)
```


## Where we are at so far.
 The django app doesn't run - I think this might be because it is expecting an older version
 I need to write the stuff to setup the database, ie create the first table (unless django can do this for us?  Flask with sqalchemy certainly can)
