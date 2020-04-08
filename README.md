# Running a remote Jupter Notebook Server on Wynton HPC
Instructions on running a jupyter notebook instance on UCSF Wynton High Performance Compute Cluster. Since wynton access utilizes SSH access, familiarize yourself on using SSH. On Mac/Linux, this can easily be access via terminal. This is enabled by default on newer versions of Windows 10. [Some older versions require extra steps](https://www.howtogeek.com/336775/how-to-enable-and-use-windows-10s-built-in-ssh-commands/).

## Wyton Access
You will need to first obtain access to the UCSF Wynton server. This can be done [here](https://wynton.ucsf.edu/hpc/about/join.html).

After obtaining access, use the [procedure to log in](https://wynton.ucsf.edu/hpc/get-started/access-cluster.html). This is done through SSH. In Mac/Linux, SSH is accessed via `Terminal`. In Windows, you're going to want to use `Powershell`.

I will be using `log2.wynton.ucsf.edu` as the main access point for this notebook. You'll generally have access to more powerful hardware on the development nodes. The way these are accessed is by entering `ssh NODE_NAME` only after successfully accessing the login node. 

e.g. the commands could be entered in the order of:

```
ssh USERNAME@log2.wynton.ucsf.edu
ssh NODE_NAME
```

Node names and specs can be found [here](https://wynton.ucsf.edu/hpc/about/specs.html). 

*Nodes are selected based on hardware needs. General coding will benefit from the Xeon E5430 CPU found on `dev1`, but using large datasets will probably require `dev2` or `dev3` which have larger `/scratch` designation. __Deep learning (and other GPU-dependent tasks) should be done on `gpudev1`.__*


### SSH Configuration
Storing a public-private ssh key pair will save a lot of headaches in the future. What this allows us to do is to login to the server without having to enter a password. This is especially useful because accessing the development nodes requires you to enter your password twice. Instructions are pulled from [here](https://www.tecmint.com/ssh-passwordless-login-using-ssh-keygen-in-5-easy-steps/).

1. __From Local Machine:__ `ssh-keygen -t rsa`
2. *`Enter file in which to save the key`*
  - Press `ENTER` (Leave Default)
3. *`Enter passphrase`* 
  - Press `ENTER` (Leave Default)
4. *`Enter passphrase again`* 
 - Press `ENTER` (Leave Default)
5. __MacOS:__
   ```
   curl https://raw.github.com/beautifulcode/ssh-copy-id-for-OSX/master/ssh-copy-id.sh -o \n
   /usr/local/bin/ssh-copy-id
   
   ssh-copy-id -i ~/.ssh/id_rsa.pub USERNAME@log2.wynton.ucsf.edu
   ```
   __Windows:__
   ```
   cat ~/.ssh/id_rsa.pub | ssh USERNAME@log2.wynton.ucsf.edu "cat >> ~/.ssh/authorized_keys"
   ```
  - If applicable: *`Are you sure you want to continue connecting? (yes/no)`* Type `yes`
  - Enter password when prompted

This allows us to access the login node without being prompted for a password. 

Access a node by entering `ssh NODE_NAME` after logging into the node. If you log in, skip to the __Setting up Jupyter Notebook Server__ section. 

If you are prompted for a password, you will need to repeat the process, this time pairing the login node to the dev:

1. `ssh USERNAME@log2.wynton.ucsf.edu`
2. `ssh-keygen -t rsa`
3. *`Enter file in which to save the key`*
  - Press `ENTER` (Leave Default)
4. *`Enter passphrase`* 
  - Press `ENTER` (Leave Default)
5. *`Enter passphrase again`* 
  - Press `ENTER` (Leave Default)
6. `ssh-copy-id -i ~/.ssh/id_rsa.pub USERNAME@NODE_NAME`
  - Enter password when prompted

Make sure the terminal window says `[USERNAME@wynlog2 ~]` during this set of steps. It is possible that you get logged out. Remember, we are creating that password pairing between your account's login node  and the development node.

## Setting up Jupyter Notebook Server

### Package Installation

Now that we have the password pairings established, we can finally begin to install our python packages. The python installation is barebones so we need to install __EVERY__ package we desire manually.

Login with 
```
ssh -t USERNAME@log2.wynton.ucsf.edu 'ssh USERNAME@NODE_NAME'
```

### Update pip

In the development node, enter:
```
pip install --user --upgrade pip
pip3 install --user --upgrade pip
```

### Install your favorite packages
UCSF has Python 2 and 3 installed, and *very little else.* You will need to install any package you intend on using via pip3 install --user PACKAGE NAME" You can copy and paste the snippit below for the essentials. I will be doing these installations on Python 3, but if you would like to use Python 2.x, simply use pip instead of pip3.

In the development node, enter: 
```
pip3 install --user numpy scipy matplotlib pandas scikit-learn
```

### Install Jupyter Notebooks
Now we're at the meat and potatoes of this long endeavor. You will be installing jupyter notebooks and configuring it to run as a server which you can access remotely. 

In the development node, enter: 
```
pip3 install --user jupyter
```

I have included a config file which you can use. Download or clone this repo and enter the following command to upload `jupyter_notebook_config`.

__From local machine (Type `exit` in Terminal):__ 
```
scp` *`CONFIG_FILE_LOCATION`* `USERNAME@log2.wynton.ucsf.edu:"~/.jupyter"
```

Fulll instructions to set up a jupyter notebook server can be found [here](https://jupyter-notebook.readthedocs.io/en/stable/public_server.html). You may configure your own config file if there are other options you'd like to set.

## Accessing the notebook server

Here's the moment you've all been waiting for. We will now initiate the notebook instance and access the server from our local machines. This is done by connection ports between your machine and the login node, and then the login node with the development node.

First decide on a port number. In the AppleScript I've attached, it generates a 4 digit number randomly. Try to avoid common ones like `8888` and `8880`. Open a fresh terminal window and type:

```
ssh -N -f -L localhost:PORT:localhost:PORT USERNAME@log2.wynton.ucsf.edu;
ssh -t USERNAME@log2.wynton.ucsf.edu 'ssh -N -f -L localhost:PORT:localhost:PORT USERNAME@NODE_NAME';
ssh -t USERNAME@log2.wynton.ucsf.edu 'ssh USERNAME@NODE_NAME'
jupyter notebook --port=PORT
```

Now just open your browser and type in `localhost:PORT` and you should be good to go.

__You will need to do this every time you intend on accessing the notebook.__ It is fairly simple to write a script to automate this. I've attached a launchable applescript for Mac in the git, which you'll need to modify with your own credentials. 

The first time you access it, you will be asked to generate a password to access the notebook.

<img src="/Images/Jupyter First Launch.jpg" width="50%">
The key can be found in the terminal window, as seen below:
<img src="/Images/Terminal View.jpg" width="80%">

From here, you can upload files via the GUI and work on your projects.
