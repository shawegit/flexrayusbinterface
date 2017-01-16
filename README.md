## Description ##
The FlexRayUsbInterface is the lowest communication layer, i.e. hardware interface.
It works through driver library [ftd2xx](http://www.ftdichip.com/Drivers/D2XX.htm).

# Dependencies #
### install libftd2xx v1.1.12 (on ubuntu) via: ###
```
#!bash
cd path/to/flexrayusbinterface
sudo dpkg -i lib/libftd2xx_1.1.12_amd64.deb
```
### install libftd2xx v1.3.6 (on fedora/arch linux) via:###
The final command in this block will generate a number of warnings or "errors". That is totally normal - works anyway.
```
#!bash
cd path/to/flexrayusbinterface/lib
tar -xvzf libftd2xx-x86_64-1.3.6.tgz 
cd release/build
sudo -s
cp libftd2xx.* /usr/local/lib
chmod 0755 /usr/local/lib/libftd2xx.so.1.3.6
ln -sf /usr/local/lib/libftd2xx.so.1.3.6 /usr/local/lib/libftd2xx.so
cd ..
cp ftd2xx.h WinTypes.h /usr/local/include/
ldconfig -v|grep ftd2xx
exit
```
### ncurses ###
```
#!bash
sudo apt-get install libncurses5-dev 
```
### cmake ###
```
#!bash
sudo apt-get install cmake
```
### ros logging ###
```
#!bash
sudo apt-get install ros-jade-rosout
```
### doxygen OPTIONAL ###
```
#!bash
sudo apt-get install doxygen
```
# Build steps #

```
#!bash
cd path/to/flexrayusbinterface
mkdir build
cd build
cmake ..
make
```

# Run it #
### NOTE: We recommend copying the udev rules file to /etc, otherwise the commandline tool can only be run with root privileges ###
```
#!bash
sudo cp path/to/flexrayusbinterface/udev/30-ftdi.rules /etc/udev/rules.d/
```
## Run the interface with ##
```
#!bash
cd path/to/flexrayusbinterface/bin
./flexrayusbinterface

```
# Documentation #
Generate a doxygen documentation using the following command:
```
#!bash
cd path/to/flexrayusbinterface
doxygen Doxyfile
```
The documentation is put into the doc folder.

# Mapbox Variant #
Get mapbox from Git, and clone it into a directory path/to/flexrayusbinterface/../Mapbox. If you do it like this, the
provided cmake findMapbox will work for you. 
```
#! bash
git clone https://github.com/mapbox/variant.git path/to/flexrayusbinterface/../Mapbox
```
Make sure you don't put it inside the CMake source directory, because target_include_directories()
does not work with include directories that are within the CMake source directory, and so you would have to label it as
private, and then add the include directory to mapbox manually in a project that uses the flexrayusbinterface library. 


# Unit Tests (Optional) #
First you need to install Googlet Test. On Ubuntu, one approach is 
```
#!bash
sudo apt-get install libgtest-dev
cd /usr/src/gtest
sudo cmake CMakeLists.txt && sudo make
sudo cp *.a /usr/lib
```

To build the project with unittesting enabled and to run the unit tests do
```
#!bash
cd path/to/flexrayusbinterface
mkdir build
cd build
cmake -DBUILD_TESTS=ON ..
make
./../bin/unit_tests
```
