# macOS build steps

The build steps for macOS align closely with those for CentOS 7, documented by Dreamworks [here](https://docs.openmoonray.org/getting-started/installation/building-moonray/building-moonray-centos-7/)

But of course, with a few tweaks.

Base requirements:
- macOS Sonoma (macOS 14)
- Install Xcode
- Download and install CMake 3.26.5
    https://github.com/Kitware/CMake/releases/download/v3.26.5/cmake-3.26.5-macos-universal.dmg
	sudo "/Applications/CMake.app/Contents/bin/cmake-gui" --install

Create a clean root folder for moonray, and follow the next three steps:
1. Create the folders
	mkdir -p /Applications/MoonRay/{installs,build,build-deps,source}
	mkdir -p /Applications/MoonRay/installs/{bin,lib,include}

2. Check out the OpenMoonRay source - alongside `installs/`
	cd /Applications/MoonRay/source
	git clone --recurse-submodules <repository>
	cd ..
	ln -s source/openmoonray/building .
	ln -s source/openmoonray .

3. Build the dependencies - alongside `source/` and `installs/`
	cd /Applications/MoonRay/build-deps
	cmake ../building/macOS
	  Only if building for Houdini, replace above with: cmake -DNO_USD=1 ../building/macOS
	cmake --build . 

4. Build MoonRay
	cd /Applications/MoonRay/openmoonray
	cmake --preset container-macOS
	  Only if building for Houdini, replace above with: cmake --preset container-macOS-houdini
	cmake --build --preset container-macOS

5. Run / test 
	source /Applications/MoonRay/installs/openmoonray/scripts/setup.sh
	cd /Applications/MoonRay/openmoonray/testdata
	moonray_gui -exec_mode xpu -info -in curves.rdla

7. Cleanup
	rm -rf /Applications/MoonRay/{build,build-deps,openmoonray/release}

HOUDINI: 

open Houdini Terminal in Applications
source /Applications/MoonRay/openmoonray/scripts/macOS/setupHoudini.sh
houdini

in the Scene View tab on the main window, change from "obj" to "stage"
click on Sphere and click on the viewport to place in on the "stage" 
on the viewport, click on "Persp" and select "Moonray", this should trigger the rendering, etc...


