# OpenBCI_Python_ROS
This is a Python software library built upon [OpenBCI_Python](https://github.com/OpenBCI/OpenBCI_Python) to run create an interface between the OpenBCI Ganglion and ROS. 
I have tested it on the Ganglion using BLE and the guide below is written for Ganglion. I have not tested it on Cyton and it may need additional tweaking to work with the Cyton board. 

## Getting Started

### Prerequisites
* Address the dependencies listed on [OpenBCI_Python](https://github.com/OpenBCI/OpenBCI_Python)
* ROS Lunar Loggerhead (may or may not work on older versions of ROS)
* Make sure that when you clone, you use the recursive flag as instructed on [OpenBCI_Python](https://github.com/OpenBCI/OpenBCI_Python)

### Setting Up
Go to your ROS workspace and create an empty package and build the package. 
Follow [Creating a ROS Package](http://wiki.ros.org/ROS/Tutorials/CreatingPackage) tutorial and [Building a ROS Package](http://wiki.ros.org/ROS/Tutorials/BuildingPackages) tutorial if you're not sure how.
Open up Terminal and move into the root directory of your ROS workspace. Gain root access using `sudo bash` and run 
```
source devel/setup.bash
```
Note: Remember to do this everytime you open a new terminal and gain root access using `sudo bash`.

Move into the `src/<your_package_name>/scripts`. Clone the all the files in this repository into your current directory. Remember to use the recursive flags as listed on [OpenBCI_Python](https://github.com/OpenBCI/OpenBCI_Python). Move into `bluepy/bluepy` and type `make`.
Open a new terminal and run `hcitool dev`. Find out the hci number of your CSR bluetooth module. Open `btle.py` from `bluepy/bluepy` and find the Scanner class definition.
```
class Scanner(BluepyHelper):
    def __init__(self,iface=1):
        BluepyHelper.__init__(self)
        self.scanned = {}
        self.iface=iface
```
Update the iface variable to the hci number of your CSR bluetooth module. For example, it should be `1` if your module is listed as hci1.

Go back to `<your_workspace>/src/<your_package_name>/scripts` . 
Make `talker.py` and `listener.py` excecutables by typing:
```
chmod +x talker.py
chmod +x listener.py
```
## Running it
Launch ROS core. 
Open new terminals and move into `<your_workspace>/src/<your_package_name>/scripts`.

### Launching the Publisher
Run `rosrun <your_package_name> talker.py --board ganglion --add talker` with root access.

This basically runs the whole [OpenBCI_Python](https://github.com/OpenBCI/OpenBCI_Python) as a ROS node. The `--add talker` adds a ROS publisher as a plugin for the OpenBCI_Python library. `talker.py` is a modified version of `user.py` from [OpenBCI_Python](https://github.com/OpenBCI/OpenBCI_Python)

Now type `/start` to start publishing data to a ROS topic.
You can stop publishing by entering `stop`.

### Launching the Subscriber
Run `rosrun <your_package_name> listener.py`

## Functionality
The talker function inside `talker.py` has all the code for publishing data. Feel free to tweak it and publish whatever data you want from the board. The function acts like the rest of the plugins from [OpenBCI_Python](https://github.com/OpenBCI/OpenBCI_Python). But instead of launching the plugins as Threads, the `talker` function is run from the main program and a seprate thread is started with the function `murder`. The `murder` thread waits for input from the user to stop streaming data. I made this ugly hack because ROS doesn't give permission to publish data to ROS Topics from threads(for the method the plugins use. I'll look into other threading solutions for ROS).  
