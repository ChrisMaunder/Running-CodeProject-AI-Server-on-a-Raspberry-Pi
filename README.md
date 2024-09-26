# Running CodeProject.AI Server on a Raspberry Pi

Building, running and debugging CodeProject.AI Server on a Raspberry Pi

This is for those brave enough to run the code directly from VS Code. If you want to simply use your RPi with a Coral stick without the fuss, check out the [Raspberry Pi (Arm64) tab in our Docker documentation](https://www.codeproject.com/ai/docs/why/running_in_docker.html#running-a-docker-container) to use CodeProject.AI Server in a docker image on Raspberry Pi with Coral.

A Raspberry Pi is a very, very modest little computer. It has a quad-core Arm64 CPU running at 1.8GHz, 4Gb DDR4 RAM, Bluetooth, Wi-Fi, some ports and not a lot else. It's cheap, it's tiny, and it can run Visual Studio Code debugging a .NET 7 app that's spawned half a dozen Python modules running AI inference.

Let's walk through this.

## Step 1: Upgrade to a 64 bit Operating System

My Raspberry Pi 400 initially came with the 32bit OS for reasons unknown to me. Let's fix this by heading to the Raspberry Pi site and [grabbing the imager](https://www.raspberrypi.com/software/).

To use the [imager](https://www.raspberrypi.com/software/), make sure you have a microSD card connected to your machine. I initially chose a 1Tb SD card ($30!) to ensure everything would fit, but that seemed to cause issues with the imager. Formatting the card prior to using the imager didn't help, so I switched to a more modest (and more reputably branded) 128Gb card.

![](https://raw.githubusercontent.com/ChrisMaunder/Running-CodeProject-AI-Server-on-a-Raspberry-Pi/master/docs/assets/CPAI_on_RPi_choose_OS.PNG)

![](https://raw.githubusercontent.com/ChrisMaunder/Running-CodeProject-AI-Server-on-a-Raspberry-Pi/master/docs/assets/CPAI_on_RPi_image_write.PNG)

Once you have run the image to install the new 64bit OS on the card, insert it into your Raspberry Pi and power up.

## Step 2: Visual Studio Code

This is the easy one, thanks to Microsoft. Open a terminal window on the Pi and type:

```cpp
sudo apt update
sudo apt install code
```

Launch VS Code, sign in, sync your settings and set it up *just so* and you are good to go. VS Code on a Raspberry Pi is a testament to both the power of the Raspberry Pi and the efficiency of VS Code.

## Step 3: CodeProject.AI Server

In VS Code, clone the [CodeProject.AI Server repository](https://github.com/codeproject/CodeProject.AI-Server) as you would normally. **Make sure you have enough room on your Raspberry Pi's SD card**. A few Gb should be plenty.

Once you've pulled the repo, you will need to set things up. Again, this is straightforward.

Open a new terminal and head to the */src* folder in the repo. The easiest way is via Terminal menu in VS Code: choose '**New Terminal**', then within the terminal window just 'cd src' and you're there. Alternatively, open a terminal from the Raspberry Pi System menu, and head to the */src* directory in the CodeProject.AI Server repository.

Once there, run

```cpp
bash setup.sh
```

### ![](https://raw.githubusercontent.com/ChrisMaunder/Running-CodeProject-AI-Server-on-a-Raspberry-Pi/master/docs/assets/cp-ai-setup.png)

### The Runtimes

The setup script is generally fast except for the excruciatingly slow process of the initial installation of Python 3.7. The Raspberry Pi's come with Python 3.9 installed, but for 3.7, you will need to compile from source. Thankfully Theo van der Sluijs has created a [script](https://itheo.tech/ultimate-python-installation-on-a-raspberry-pi-and-ubuntu-script) that handles this for us. It took nearly an hour for it to complete on my Pi, with the vast majority of the time was spent running the regression tests.

After Python comes .NET 7, which is handled by another [script](https://www.petecodes.co.uk/install-and-use-microsoft-dot-net-7-with-the-raspberry-pi/), thanks to Pete Gallaghar.

Don't worry - you don't need to download anything: the *setup.sh* script has everything you need and will do this for you.

### The Modules

Once the runtimes are setup, the module installation is the same as any other platform. Just let the scripts do its thing. The Python virtual environments will be setup for each module that requires one, and the models that each module will run will be downloaded and placed in the correct location.

## Building and Debugging

Once the setup script has been run, it's time to build and launch. Again, this is a bit of a non-event because you just go to the **Run and Debug** panel in VS Code and choose **Build All & Launch Server Arm64** from the dropdown.

![](https://raw.githubusercontent.com/ChrisMaunder/Running-CodeProject-AI-Server-on-a-Raspberry-Pi/master/docs/assets/cp-ai-launch-rpi.png)

We have separate x64 and Arm64 builds because some modules, such as `PortraitFilter`, which use `Microsoft.ML.OnnxRuntime` aren't supported in Arm64 and so need to be excluded from the Arm64 build.

Clicking the arrow next to the dropdown will launch the server. You can debug and step through the server code on a Raspberry Pi just like any other VS Code installation. On launch, the same dashboard UI appears, and you can open the same explorer experience.

![](https://raw.githubusercontent.com/ChrisMaunder/Running-CodeProject-AI-Server-on-a-Raspberry-Pi/master/docs/assets/cp-ai-dash-ri.png)

Again, we need to remember that this is a tiny computer with a very limited amount of RAM. I tend to use the smallest models that a given module will allow me to use, but even then, inference times can be over a second.

![](https://raw.githubusercontent.com/ChrisMaunder/Running-CodeProject-AI-Server-on-a-Raspberry-Pi/master/docs/assets/CP-AI-RPi-inference.png)

## Improving Performance

The next step in developing for the Raspberry Pi will be tackled using three approaches:

1. Sourcing new modules that are faster, leaner, and more tuned to a resource limited environment like a Pi
2. Providing smaller and leaner models
3. Providing support for external AI accelerators such as the [Coral AI USB stick](https://coral.ai/products/accelerator/)

For now, though, we have the ability to debug issues with Python packages, .NET installs, server quirks, hardware reporting and our setup scripts natively on the Pi. This makes targeting the Raspberry Pi exceptionally easy, as long as one is patient.
