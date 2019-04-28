# PyTorch .whl for generic aarch64 platforms

Just download the file or clone the repository, and install the wheel using pip.

`pip3 install torch-1.1.0a0+472be69-cp36-cp36m-linux_aarch64.whl`

## Rationale

I wanted to run some PyTorch models on my Ultra96 board for eventual use in Xilinx/Deephi's DNNDK platofrm, which allows you to tkae a trained model and speed up inference using the DPU in the programmable logic of the ZU+ device.

## Build Instructions

In case you you have a differnt version of python, or any other reason you might have to build it yourself, ill describe the steps below.

### Memory Considerations

The biggest challenge building a large library like this on an embeedde device is the limitaion of RAM (Ultra96 has 2GB). To combat this, you could make some extra swap space, or you could limit jobs the number of jobs used when building. I would recommned making sure you have at least 1GB swap and limiting the jobs to 1, as just using a lot of swap will slow things to a standstill if you dont have lightning fast swap.

I had a spare 2GB USB2 flash drive which would work perfectly, so I used that as my extra swap. Make sure to change the /dev/sda1 to your device, and make sure its not mounted when you start this process.

First, make the swap space `sudo mkswap /dev/sda1`

edit your fstab to include the new drive. `nano /etc/fstab`, comment out any lines you see (replace them once you are done) and add `/dev/sda1 swap swap` at the top of the file. 

Turn on the new swap space `sudo swapon -p 32767 /dev/sda1`

Check it worked using `cat /proc/swaps` and seeing if your drive is listed.

Finally, set the max number of jobs. I did 1 to be safe, but if you want it to go faster you could try 2.

`export MAX_JOBS=1`

### download dependencies and clone PyTorch

Dependencies:

`sudo apt install libopenblas-dev libblas-dev m4 cmake cython python3-dev python3-yaml python3-setuptools`

Clone PyTorch:

`git clone --recursive https://github.com/pytorch/pytorch` and `cd pytorch`

Set build options for embeeded machine (no cude, no MKL, etc)

`export NO_CUDA=1`
`export NO_DISTRIBUTED=1`
`export NO_MKLDNN=1`
`export NO_NNPACK=1`
`export NO_QNNPACK=1`

and finally, run the build.

`python3 setup.py build`

Once it completes with no errors, you can run `python3 setup.py install` to install the files, and if you are reading this in the future with an updated version, you can also run `python3 setup.py bdist_wheel` to create a whl file for distribution. That command will put the generated .whl file in the newly created dist directory.

Now you can run torch!

`cd
python3
import torch`


