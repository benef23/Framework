# Framework

Installation
============

The following sections guides you through the installation of the
framework.

Dependencies
------------

Install the dependencies:

-   install [ROS
    Kinetic](http://wiki.ros.org/kinetic/Installation/Ubuntu)

-   sudo apt-install ros-kinetic-qt-build

-   sudo apt install gcc-arm-linux-gnueabihf g++-arm-linux-gnueabihf

-   sudo apt install protobuf-compiler

Cloning the repository
----------------------

Create an installation folder and clone the repository:

-   mkdir framework

-   cd framework

-   git init

-   git clone https://github.com/morpana/Framework

-   git submodule update --init --recursive

Installing PowerLink
--------------------

Build the openPowerLink libraries for both “debug” and "release.

-   cd
    {base\_dir}/framework/src/roboy\_powerlink/openPowerLink/stack/build/linux

-   cmake -DCMAKE\_BUILD\_TYPE=Debug ../..

-   make

-   make install

-   cmake -DCMAKE\_BUILD\_TYPE=Release ../..

-   make

-   make install

Refer to:
http://openpowerlink.sourceforge.net/doc/2.5/2.5.2/page\_build\_stack.html

Building the project
--------------------

Build the project using catkin\_make:

-   cd {base\_dir}/framework/

-   catkin\_make

Data
----

A set of existing obtained behaviors is stored in
src/dep\_gui/dep\_matrices. To use these behaviors, copy the
dep\_matrices folder to ~/dep\_matrices (i.e. ’home’ folder).

The data for the weights of the neural networks in the dep\_mem\_node
folder need to be downloaded and placed in the correct folder. (This is
due to file size limitations on github).

-   Go to
    <https://drive.google.com/open?id=1HMTVrxqZm0NWpZsjphFcFbcc1Lc5b31a>

-   download the folder

-   place the contents of the folder in “src/dep\_mem\_node/data/”

Controlling the arm
===================

The following section describes setting up the arm and ROS nodes to be
able to sequentially combine behaviors via the memory node.

Tab 1: roscore
--------------

Start the ROS master node.

-   roscore

Note: do not run this as sudo.

Tab 2: FPGA controller
----------------------

Connect the FPGA and your computer to a router. Scan the network to find
the IP of the FPGA:

-   sudo nmap -nsP 192.168.0.255/24

The correct IP belongs to “Gandalf”. Now connect to the FPGA via SSH:

-   ssh -X root@ip

Make sure the master node is set correctly in .bashrc:

-   export ROS\_MASTER\_URI=http://ROS\_MASTER\_IP:11311

where “ROS\_MASTER\_IP” refers to the IP of the computer running
roscore. Now run the controller:

-   ./roboy\_controlled\_node\_fpga

and choose option “eth0”.

Tab 3: roboy\_dep node
----------------------

Run the roboy\_dep node:

-   cd <span>base\_dir</span>/framework/

-   source devel/setup.bash

-   rosrun roboy\_dep roboy\_dep

Once this initializes, make sure the program goes through a sequence of
numbers. If not, reboot “./roboy\_controlled\_node\_fpga” on the FPGA.

Tab 4: dep\_gui node
--------------------

Run the dep\_gui node:

-   cd <span>base\_dir</span>/framework/

-   source devel/setup.bash

-   rosrun dep\_gui dep\_gui

The arm can now be initialized. Follow the initialization instructions
presented in the GUI. With the arm initialized, the GUI can be used to
learn new behaviors by enabling “update mode”, or can be used to replay
previously learned behaviors.

Tab 5: dep\_memory node
-----------------------

Once the arm is initialized, the learned behaviors can be used to
perform sequential behavior combination. Run the memory node:

-   cd <span>base\_dir</span>/framework/

-   source devel/setup.bash

-   rosrun dep\_memory memory.py

The behaviors can be selected on the right-hand side of the second tab
of the GUI.

Training the memory node for new behaviors
==========================================

New behaviors can be learned via the update mode in the GUI. To train
the memory node to use behaviors learned via the GUI, an example is
provided for the LAM behavior network, the trigger network and the
Hopfield behavior network in “src/dep\_mem\_node/training.ipynb”. New
behaviors could be added manually to these examples to expand the
repertoire. However, an additional consideration is the dependency upon
a recording of the behavior. This too needs to be obtained and
formatted to a single period as the three behaviors shown in
“src/dep\_mem\_node/data/bases”.
