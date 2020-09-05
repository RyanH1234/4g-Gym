# NativeScript-Vue Application

> A native application built with NativeScript-Vue

## Usage

``` bash
# Install dependencies
npm install

# Preview on device
tns preview

# Build, watch for changes and run the application
tns run

# Build, watch for changes and debug the application
tns debug <platform>

# Build for production
tns build <platform> --env.production

```

## Setup


SETTING UP VUE NATIVESCRIPT

- It's actually pretty easy compared to other projects I've had to deal with...


1 

	Firstly, install all dependencies outlined here :

	https://nativescript-vue.org/en/docs/getting-started/installation/

1.5

	Here's a quick start quide too if you're feeling ambitious :

	https://nativescript-vue.org/en/docs/getting-started/quick-start/

2

	Now we need an emulator to run our code in...

	e.g. to run this line of code : tns run android --bundle

	I went through the painful process of installing the Android SDK + the emulator through Android Studio - probably best way to do it this way, however 	ensure your AntiVirus doesn't delete any important files (like ABD)

3

	Now let's run this emulator

	Android SDK Location : C:\Users\Ryan\AppData\Local\Android\Sdk

	Android Emulator Location : C:\Users\Ryan\AppData\Local\Android\Sdk\emulator

	to see a list of available devices -> tns device Android --available-devices

	to see a list of connected devices -> tns device 

	run the following lines : 

		cd C:\Users\Ryan\AppData\Local\Android\Sdk\emulator

		emulator.exe -avd Pixel_2_API_Q

 
4
	
	In a seperate command window to 4. re-direct to your project
	
	tns run --clean android --bundle
	

RUNNING ON PHYSICAL DEVICE...

1. https://developer.android.com/training/basics/firstapp/running-app - follow this up until the point it starts talking about running the app in 'Android Studio'

1. Ensure USB debugging is selected in 'Developer Options' of phone

2. In cmd window : 
	cd 'PROJECT_PATH'	
	tns device
	tns run android --device 521095204752c3ed


        // get mCi, mPci
        const cellInfo = manager.getAllCellInfo().toString().split(" ")
        let mCi = cellInfo[4].split("=")[1];
        let mPci = cellInfo[5].split("=")[1];

        // let signalStrength = manager.getSignalStrength().toString();
        // let data = signalStrength.split(":")[2].split(",")[0].split(" ");
        // data = this.toObject(data, "=");



                // console.dir(manager.getAllCellInfo().toString());
        // // let signalStrength = manager.getSignalStrength().toString();
        // // let data = signalStrength.split(":")[2].split(",")[0].split(" ");
        // // data = this.toObject(data, "=");
        // let data = {};

                // let cellinfogsm = manager.getAllCellInfo().get(0);
        // let cellSignalStrengthGsm = cellinfogsm.getCellSignalStrength();
        // const signalStrengthDBM = cellSignalStrengthGsm.getDbm();
        // const rssiASU = cellSignalStrengthGsm.getAsuLevel();
