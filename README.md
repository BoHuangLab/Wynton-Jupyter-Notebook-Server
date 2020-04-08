# Jupyter-Notebook-Wynton
Instructions on running a jupyter notebook instance on UCSF Wynton Supercompute Cluster. Since wynton access utilizes SSH access, familiarize yourself on using SSH. On Mac/Linux, this can easily be access via terminal. [On PC, this requires some extra steps.] (https://www.howtogeek.com/336775/how-to-enable-and-use-windows-10s-built-in-ssh-commands/)

## Wyton Access
You will need to first obtain access to the UCSF Wynton server. This can be done [here.] (https://wynton.ucsf.edu/hpc/about/join.html)

After obtaining access, use the [procedure to log in] (https://wynton.ucsf.edu/hpc/get-started/access-cluster.html). 

I will be using `log2.wynton.ucsf.edu` as the main access point for this notebook. You'll generally have access to more powerful hardware on the development nodes. The way these are accessed is by entering `ssh NODE_NAME` only after successfully accessing the login node. 

e.g. the commands could be entered in the order of:

1. `ssh USERNAME@log2.wynton.ucsf.edu`
2. ENTER PASSWORD
3. `ssh NODE_NAME`
4. ENTER PASSWORD

Node names and specs can be found [here] (https://wynton.ucsf.edu/hpc/about/specs.html). Nodes are selected based on hardware needs. General coding will benefit from the Xeon E5430 CPU found on "dev1", but using large datasets will probably require `dev2` or `dev3` which have larger "/scratch" designation. Deep learning (and other GPU-dependent tasks) should be done on `gpudev1`


### SSH Configuration
Storing a public-private ssh key pair will save a lot of headaches in the future. What this allows us to do is to login to the server without having to enter a password. This is especially useful because accessing the development nodes requires you to enter your password twice. Instructions are pulled from [here] (https://www.tecmint.com/ssh-passwordless-login-using-ssh-keygen-in-5-easy-steps/).

1. Open ssh terminal 
2. Enter `ssh-keygen -t rsa`
2. `Enter file in which to save the key` 
-Press "ENTER" (Leave Default)
3. `Enter passphrase` 
-Press "ENTER" (Leave Default)
4. `Enter passphrase again` 
-Press "ENTER" (Leave Default)
5. `ssh USERNAME@log2.wynton.ucsf.edu mkdir -p .ssh`
-Enter password when prompted
6. `cat .ssh/id_rsa.pub | ssh USERNAME@log2.wynton.ucsf.edu '>>cat .ssh/authorized_keys'`
- Enter password when prompted
7. `ssh USERNAME@log2.wynton.ucsf.edu "chmod 700 .ssh; chmod 640 .ssh/authorized_keys"`
- Enter password when prompted

This allows us to access the login node without being prompted for a password. __We will need to repead this process to access the development node in a similar fashion__ You should do this for every development node you intend to use frequently.

1. `ssh USERNAME@log2.wynton.ucsf.edu`
2. Enter `ssh-keygen -t rsa`
2. *`Enter file in which to save the key`*
-Press "ENTER" (Leave Default)
3. *`Enter passphrase`* 
-Press `"ENTER"` (Leave Default)
4. *`Enter passphrase again`* 
-Press `"ENTER"` (Leave Default)
5. `ssh USERNAME@log2.wynton.ucsf.edu mkdir -p .ssh`
-Enter password when prompted
6. `cat .ssh/id_rsa.pub | ssh USERNAME@log2.wynton.ucsf.edu '>>cat .ssh/authorized_keys'`
- Enter password when prompted
7. `ssh USERNAME@log2.wynton.ucsf.edu "chmod 700 .ssh; chmod 640 .ssh/authorized_keys"`
- Enter password when prompted

Make sure the terminal window says "[username@log2.wynton.ucsf.edu]" during this set of steps. It is possible that you get logged out. Remember, we are creating that password pairing between your account's login node  and the development node.

## Setting up Jupyter Notebook Server

### Package Installation

Now that we have the password pairings established, we can finally begin to install our python packages. The python installation is barebones so we need to install __EVERY__ package we desire manually.

Login with `ssh -t USERNAME@log2.wynton.ucsf.edu 'ssh USERNAME@NODE_NAME'`

### Update PIP

In the development node, enter:
`pip install --user --upgrade pip`
`pip3 install --user --upgrade pip`

### Install your favorite packages
UCSF has Python 2 and 3 installed, and *very little else.* You will need to install any package you intend on using via pip3 install --user PACKAGE NAME" You can copy and paste the snippit below for the essentials. I will be doing these installations on Python 3, but if you would like to use Python 2.x, simply use pip instead of pip3.

In the development node, enter: `pip3 install --user numpy scipy matplotlib pandas scikit-learn`

### Install Jupyter Notebooks
Now we're at the meat and potatoes of this long endeavor. You will be installing jupyter notebooks and configuring it to run as a server which you can access remotely. 

In the development node, enter: `pip3 install --user jupyter`

I have included a config file which you can use. You may configure your own if there are other options you'd like to set by following the instructions here. Download the file and enter this command to upload it to the right folder.

`scp` *`CONFIG_FILE_LOCATION`* *`USERNAME@log2.wynton.ucsf.edu:"~/.jupyter"`

Fulll instructions to set up a jupyter notebook server can be found [here] (https://jupyter-notebook.readthedocs.io/en/stable/public_server.html).

## Accessing the notebook server

Here's the moment you've all been waiting for. We will now initiate the notebook instance and access the server from our local machines. This is done by connection ports between your machine and the login node, and then the login node with the development node. __You will need to do this every time you intend on accessing the notebook.__ It is fairly simple to write a script to automate this. I've attached an applescript for Mac in the git, which you'll need to update with your own credentials. 

First decide on a port number. In the AppleScript I've attached, it generates a 4 digit number randomly. Try to avoid common ones like "8888" and "8880." Open a fresh terminal window and type:

`ssh -N -f -L localhost:PORT:localhost:PORT USERNAME@log2.wynton.ucsf.edu;`
`ssh -t USERNAME@log2.wynton.ucsf.edu 'ssh -N -f -L localhost:PORT:localhost:PORT USERNAME@gNODE_NAME`
`ssh -t USERNAME@log2.wynton.ucsf.edu 'ssh USERNAME@NODE_NAME`
`jupyter notebook --port=PORT`

Now just open your browser and type in `localhost:PORT` and you should be good to go.  

[The first time you access it, you will be asked to generate a password to access the notebook.](/Images/Jupyter First Launch.jpg?raw=true "Title")

The key can be found in the terminal window, as seen below:

From here, you can upload files via the GUI.
