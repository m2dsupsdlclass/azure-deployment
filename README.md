# Azure Usage Instructions

Microsoft is partnering with [UPS Master 2 Data Science](
http://datascience-x-master-paris-saclay.fr/) to grant students
free GPU compute time for the Deep Learning class.

## Setting up your account

Create a Microsoft account to connect to:

https://portal.azure.com

Try to login with the email address of your choice. The page should
update to offer you the ability to create your account for that email
address.

Note that some students have reported that some school email addresses
failed to receive the confirmation code required to activate your
account.

Once you can login to https://portal.azure.com please contact Olivier or
Charles on slack (or by email) and give us the email address you used to
login to the azure interface. Let's use the following slack room to
setup accounts:

https://m2dsupsdlclass.slack.com/messages/azureaccounts/

You need to be a registered student of the master to access this slack
channel.

We will grant you access to the "Microsoft Azure DeepNet" subscription
that has credits for approximately 20,000 hours of GPU compute time for
the class.

Once we grant you the access, you will probably arrive on a page, that
states a message similar to "No available application, please contact
your administrator." Ignore this message and instead go back to
https://portal.azure.com, log out and log back in to the portal
interface, in the left-hand side toolbar, click at the bottom on "More
services" and then "Subscriptions". You should see:

"Microsoft Azure DeepNet"

If not, click on the top and right-hand side menu with your name and
switch directory to the one named "ericmoulinesoutlook.onmicrosoft...".
Then check again the subscription menu. If you still cannot access the
subscription please contact us on slack.


## Creating your own Virtual Machine (VM)

We prepared a VM image with everything pre-installed (Python, CUDA,
CuDNN, jupyter, keras, tensorflow-gpu 0.12.1, etc) along with a local
copy of the dogs-vs-cats dataset.

You can use this image to build your own VM. When doing so, please
**name the resource group and the VM it-self as "firstname-lastname"**
(using only lowercase ACSII characters) to make it easy to know which VM
is owned by you. Here are the parameters to use:

To deploy your own VM using this image, click on the following link:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fm2dsupsdlclass%2Fazure-deployment%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

Here are the **important parameters**:

- **Subscription**: "Microsoft Azure DeepNet" (should be the default);
- **Resource group**:
  - tick "create new" (if this is the first time you deploy a VM), then:
  - `firstname-lastname` (only lowercase ascii characters);
- **Location**: choose "South Central US";
- **VM name**: `firstname-lastname` (as for the resource group);
- **Admin User Name**: a username of your choice, will be required for ssh;
- **Admin Password**: it should be long enough, will be required for ssh.

Feel free to pin this deployment to your dashboard to make it easy to
find the next time you log in to the Azure portal.

You can leave the network configuration parameters to their default
values.

If it does not work, please click on the top and righthand-side menu
with your name and switch directory to the one named
"ericmoulinesoutlook.onmicrosoft..." and then try to click on the link
again.

If it still does not work, please contact Olivier or Charles preferably
on slack.

Once you are no longer using the VM, **don't forget to stop it!** (see
below).


## Connecting to your VM

Once your VM is deployed, copy the IP address and from the Azure portal
info you should be able to ssh into it:

    ssh username@ip-address

If your laptop is under Windows, feel free to use
[PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/) to connect
to your Azure VM on port 22.

**The first time you log in to your VM**: initialize your home folder
with the following script:

    source /workspace/workspace_setup.sh

You should then be able to import keras from the `python` command in
your `PATH`:

    $ python -c "import keras"
    Using TensorFlow backend.
    I tensorflow/stream_executor/dso_loader.cc:128] successfully opened CUDA library libcublas.so locally
    I tensorflow/stream_executor/dso_loader.cc:128] successfully opened CUDA library libcudnn.so locally
    I tensorflow/stream_executor/dso_loader.cc:128] successfully opened CUDA library libcufft.so locally
    I tensorflow/stream_executor/dso_loader.cc:128] successfully opened CUDA library libcuda.so.1 locally
    I tensorflow/stream_executor/dso_loader.cc:128] successfully opened CUDA library libcurand.so locally

For some unknown reason this is sometime very slow, but apprently it
gets better over time when you actually use the VM for heavy GPU
computation (e.g. when training models in a notebook).

Feel free to use the `tmux` command to get a session that you can
re-attach to and launch long running commands such as `jupyter
notebook. Learn about `tmux` here:

http://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/

## Starting jupyter

Upload your notebook either into your home folder `/home/username` or in
the `/workspace` folder. Under Windows you can use PuTTY, under Linux
and macOS you can use the `scp` command in a terminal.

Note that the notebook for lab #3 is already available in `/workspace`.

Ensure that you have no jupyter running on port 8888 on your local
laptop, then open a ssh tunnel to your Azure VM:

    ssh -L 8888:localhost:8888 username@ip-address

Alternatively use PuTTY to setup a ssh tunnel on port 8888 both on your
local machine and the remote Azure VM.

Then on the VM (preferably under a `tmux` session), `cd` into the folder
where you uploaded the notebooks (e.g. `/workspace`) and type:

    jupyter notebook

This should print the URL with a `localhost:8888` address and a unique
security token. Copy the full URL into the URL bar of browser window
running on your laptop and you should be good to go.


## Monitoring GPU Usage

In a shell on the VM you can launch the following command to monitor the
usage of the GPU on your host

    watch nvidia-smi

The `ResNet50` model should be able to process 40+ images per second on
the NC6 VMs with a K80 GPU. Sometimes it is slower at the beginning but
the processsing speed tend to increase when you actively use the
machine.

## Stopping your VM

In the Azure portal (https://portal.azure.com), please click on the
**stop** button of your own VM whenever you do not need it anymore. It
will be possible to restart it later with all your files there as you
left them.

No compute credit will be used when the VM is stopped in the
"deallocated" state.

FYI a single GPU (NC6) VM costs approximately $1 / hour. Feel free to
use it as much as you want but **don't waste it by leaving it launched
when you don't use it anymore**.
