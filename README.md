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

https://m2dsupsdlclass-2018.slack.com/messages/C8PTYNF9C/

You need to be a registered student of the master to access this slack
channel.

We will grant you access to the "AZUREDEEPNETOG" subscription
that has credits for approximately 20,000 hours of GPU compute time for
the class.

Once we grant you the access, you will probably arrive on a page, that
states a message similar to "No available application, please contact
your administrator." Ignore this message and instead go back to
https://portal.azure.com, log out and log back in to the portal
interface, in the left-hand side toolbar, click at the bottom on "More
services" and then "Subscriptions". You should see:

"AZUREDEEPNETOG"

If not, click on the top and right-hand side menu with your name and
switch directory to the one named "ericmoulinesoutlook.onmicrosoft...".
Then check again the subscription menu. If you still cannot access the
subscription please contact us on slack.


## Creating your own Virtual Machine (VM)

We will used the Azure Deep Learning VM image that comes with everything
pre-installed (Python, CUDA, CuDNN, jupyter, keras, tensorflow-gpu,
 etc).

You can use this image to build your own VM. When doing so, please
**name the resource group and the VM it-self as "firstname-lastname"**
(using only lowercase ACSII characters) to make it easy to know which VM
is owned by you.

To deploy your own VM using this image, follow those steps in the
portal:

- click "+ New" in the left hand size panel;
- search for "Deep Learning Virtual Machine" and click on the "Create"
  button;
- **name**: "firstname-lastname" (only lowercase ascii characters);
- **OS type**: "Linux";
- choose a username and a password (write them down, you will need them
  later);
- **subscription**: "AZUREDEEPNETOG" (should be the default);
- **resource group**:
  - tick "create new" (if this is the first time you deploy a VM), then:
  - `firstname-lastname` (only lowercase ascii characters);
- **location**: choose "East US" should work;
- **virtual machine size**: "1x Standard NC6";
- use the default storage account;
- in the "Summary" panel, wait a bit for the validation and click "OK";
- in the "Buy" panel, tick the checkbox and click "OK" (after reading
  the boring legal text off-course);

Feel free to pin this deployment to your dashboard to make it easy to
find the next time you log in to the Azure portal.

You can leave the network configuration parameters to their default
values.

If it does not work, please click on the top and righthand-side menu
with your name and switch directory to the one named
"ericmoulinesoutlook.onmicrosoft..." and then try again.

If it still does not work, please contact Olivier or Charles preferably
on slack.

Once you are no longer using the VM, **don't forget to stop it!** (see
below).


## Connecting to your VM

Once your VM is deployed (wait a couple of minutes for the VM to start
and be ready to accept connections), copy the IP address and from the
Azure portal info you should be able to ssh into it:

    ssh username@ip-address

If your laptop is under Windows, feel free to use
[PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/) to connect
to your Azure VM on port 22.

Make sure to update setuptools:

    pip install -U pip setuptools

The upgrade `keras` and `tensorflow-gpu` (the GPU version) and  `kaggle-cli`
which we will need later

    pip install -U tensorflow-gpu==1.4.1 keras kaggle-cli

Note that tensorflow 1.5 or later do not seem to work on that VM at the
time of writing.

If you prefer the new experimental jupyterlab over the traditional
jupyter notebook interface you can install the latest version with pip:

    sudo /anaconda/bin/pip install -U jupyterlab

You should then be able to import keras from the `python` command in
your `PATH`:

    $ python -c "import keras"
    Using TensorFlow backend.
    2018-02-08 16:12:58.121025: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
    2018-02-08 16:12:58.361406: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties: 
    name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
    pciBusID: d489:00:00.0
    totalMemory: 11.17GiB freeMemory: 11.10GiB
    2018-02-08 16:12:58.361473: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: d489:00:00.0, compute capability: 3.7)


For some unknown reason this is sometime very slow, but apprently it
gets better over time when you actually use the VM for heavy GPU
computation (e.g. when training models in a notebook).

Feel free to use the `tmux` command to get a session that you can
re-attach to and launch long running commands such as `jupyter
notebook`. Learn about `tmux` here: [a quick and easy guide to tmux](
http://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/).

## Starting jupyter

Upload your notebook either into your home folder `/home/username`.
Under Windows you can use PuTTY, under Linux and macOS you can use the
`scp` command in a terminal.

You can also clone the official repo:

    git clone https://github.com/m2dsupsdlclass/lectures-labs

Ensure that you have no jupyter running on port 8888 on your local
laptop, then open a ssh tunnel to your Azure VM:

    ssh -L 8888:localhost:8888 username@ip-address

Alternatively use PuTTY to setup a ssh tunnel on port 8888 both on your
local machine and the remote Azure VM.

Then on the VM (preferably under a `tmux` session), `cd` into the folder
where you uploaded the notebooks and type:

    jupyter notebook --no-browser

or alternatively:

    jupyter lab --no-browser

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
