# Songscape deployment via Ansible WIP

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

## Optional use vagrant rather than EC2 for dev.

Setup the VM
```
vagrant up
```

This will run the site.yml playbook as well.

Run the site.yml playbook against then VM when needed and it's up
```
ansible-playbook --private-key=~/.vagrant.d/insecure_private_key -u vagrant -i .vagrant/provisioners/ansible/inventory/vagrant_ansible_inventory site.yml
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

Make sure you have copied your ssh key to keys/ (or use your normal process).  Run chmod 600 on the key (you only need to do this once).  You will also need to change the ssh key in hosts to match your ssh key

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
ssh -i keys/andre andre@54.66.160.212 #to access the dev server
ssh -i keys/andre andre@vuwunicohalo001.vuw.ac.nz # to access the prod server (you will need to VPN to the uni network first)
```

# Installing locally inside a virtualbox VM for development.
* Install Atom -> download thee .deb file and install it
* Clone this repo somewhere convenient
* install the lastest version of ansible inside a python virtualenv

```bash
virtualenv venv  # do this in the directory you want to run ansible from, it doesn't really matter Where
source venv/bin/activate #activate virtual environment
pip install ansible
sudo adduser songscape # this is usually handled by ansible but for local dev in a virtualbox we will do this by hand for now
```

  * Run the ansible script against local machine (the virtualbox VM)
```bash
ansible-playbook -u victor -K -i hosts site.yml --limit local
```

  * Deactivate virtual environment ```deactivate```
  * Change directory to the songscape install location /opt/songscape.
  * git status should indicate you are on the vuw branch
  * copy the recordings to /opt/test_data

## Workflow

* always git pull first - make sure you are up to date
* make changes
  * test that they work.
* git pull again - in case someone else has made changes to the same file - try not to do this as you'll need to resolve conflicts
* git add changes
* git commit -m "Meaningful message"
* git push origin vuw
