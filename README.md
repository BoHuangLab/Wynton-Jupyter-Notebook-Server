# Jupyter-Notebook-Wynton
Instructions on running a jupyter notebook instance on UCSF Wynton Supercompute Cluster. Since wynton access utilizes SSH access, familiarize yourself on using SSH. On Mac/Linux, this can easily be access via terminal. On PC, this requires some extra steps [[https://www.howtogeek.com/336775/how-to-enable-and-use-windows-10s-built-in-ssh-commands/]].

## Wyton Access
You will need to first obtain access to the UCSF Wynton server. This can be done here: https://wynton.ucsf.edu/hpc/about/join.html

After obtaining access, use the procedure [[https://wynton.ucsf.edu/hpc/get-started/access-cluster.html]] to log in. I will be using log2.wynton.ucsf.edu as the main access point for this notebook. You'll generally have access to more powerful hardware here. The way these are accessed is by entering "ssh /development node name/." after successfully accessing the login node. e.g. the commands could be entered in the order of:

1. ssh USERNAME@log2.wynton.ucsf.edu
2. ENTER PASSWORD
3. ssh NODE NAME
4. ENTER PASSWORD

Node names and specs can be found here [[https://wynton.ucsf.edu/hpc/about/specs.html]]. Nodes are selected based on hardware needs. General coding will benefit from the Xeon E5430 CPU found on "dev1", but using large datasets will probably require "dev2" or "dev3" which have larger "/scratch" designation. Deep learning (and other GPU-dependent tasks) should be done on "gpudev1"


## SSH Configuration
Storing a public-private ssh key pair will save a lot of headaches in the future. What this allows us to do is to login to the server without having to enter a password. This is especially useful because accessing the development nodes requires you to enter your password twice. Instructions are pulled from here https://www.tecmint.com/ssh-passwordless-login-using-ssh-keygen-in-5-easy-steps/

1. Open ssh terminal and enter: ssh-keygen -t rsa
2. "Enter file in which to save the key" Press "ENTER"
3. Enter passphrase - Leave blank and press "Enter"
4. Repeat
7. Type: ssh USERNAME@log2.wynton.ucsf.edu mkdir -p .ssh
8. Enter password when prompted
9. cat .ssh/id_rsa.pub | ssh USERNAME@log2.wynton.ucsf.edu '>>cat .ssh/authorized_keys'
10. Enter password when prompted
11. ssh USERNAME@log2.wynton.ucsf.edu "chmod 700 .ssh; chmod 640 .ssh/authorized_keys"
12. Enter password when prompted

This allows us to access the login node without being prompted for a password. *We will need to repead this process to access the development node in a similar fashion* You should do this for every development node you intend to use frequently.

1. ssh USERNAME@log2.wynton.ucsf.edu
1. ssh-keygen -t rsa
2. "Enter file in which to save the key" Press "ENTER" (Leave Default)
3. Enter passphrase - Leave blank and press "Enter"
4. Repeat
7. Type: ssh NODE NAME mkdir -p .ssh
8. Enter password when prompted
9. cat .ssh/id_rsa.pub | ssh NODE NAME '>>cat .ssh/authorized_keys'
10. Enter password when prompted
11. ssh NODE NAME "chmod 700 .ssh; chmod 640 .ssh/authorized_keys"
12. Enter password when prompted

Make sure the terminal window says "[username@log2.wynton.ucsf.edu]" during this set of steps. It is possible that you get logged out. Remember, we are creating that password pairing between your account's login node  and the development node.

## Package Installation

Now that we have the password pairings established, we can finally begin to install our python packages. The python installation is barebones so we need to install EVERY package we desire manually.

Login with "ssh -t USERNAME@log2.wynton.ucsf.edu 'ssh USERNAME@NODE NAME'"

### Update PIP ###
"pip install --user --upgrade pip"
"pip3 install --user --upgrade pip"

### Install your favorite packages
UCSF has Python 2 and 3 installed, and *very little else* You will need to install any package you intend on using via pip3 install --user PACKAGE NAME" You can copy and paste the snippit below for the essentials. I will be doing these installations on Python 3, but if you would like to use Python 2.x, simply use pip instead of pip3.

"pip3 install --user numpy scipy matplotlib pandas scikit-learn"

##Install Jupyter Notebooks
Now we're at the meat and potatoes of this long endeavor. You will be installing jupyter notebooks and configuring it to run as a server which you can access remotely. Instructions are here [[https://jupyter-notebook.readthedocs.io/en/stable/public_server.html]]

Enter "pip3 install --user jupyter"

Now we will access the jupyter config file.

