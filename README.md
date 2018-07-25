# Notes-for-install-opencv-python-onto-MacOS
This Rep is only a notes for myself of installing opencv and python2/3 onto MacOS. I meet some problems during the installation so I believe it would be better for me to make a record about how did I solved thoes problems.

## Background:
   Macbook Pro 2018  
   Date: 2018-07-24  
   OS: MacOS High Sierra 10.13.6  
   Python2 version: 2.7.15  
   Python3 version: 3.7.0  
   opencv version: 3.4.2  
   Xcode version: 9.4.1

## Reference:
- [Install OpenCV 3 on macOS with Homebrew (the easy way) by Adrian Rosebrock](https://www.pyimagesearch.com/2016/12/19/install-opencv-3-on-macos-with-homebrew-the-easy-way/)
- [Install OpenCV 3 on MacOS by VAIBHAW SINGH CHANDEL](https://www.learnopencv.com/install-opencv3-on-macos/)

## Steps:
all code are terminal command 
### 1. Install Xcode
   The installation is simple and straightforward. But the accepting license part was not.
   
The tutorial I went through told me to run to  accepting license part after the installation is done.
```shell
sudo xcodebuild -license accept
```
But I got an error msg as
```shell
xcode-select: error: tool 'xcodebuild' requires Xcode, but active developer directory '/Library/Developer/CommandLineTools' is a command line tools instance
```
I solved it by run the following command which update Xcode.  
Remember to "SHUT DOWN" your Mac instead of "RESTART" to finish this update.  
The update will automatically power up your Mac if you choose "SHOTDOWN" but will be canceled if you "RESTART"
```shell
softwareupdate --list
softwareupdate --install -a
```
After the update, run 
```shell
sudo xcodebuild -license accept
```
and accept the license again to finish this step.

### 2. Install Homebrew
This step did get me any error. Just use the following commands.
```shell
ruby -e "$(curl -fsSL 
https://raw.githubusercontent.com/Homebrew/install/master/install)"
 
# update homebrew 
brew update
  
# Add Homebrew path in PATH
echo "# Homebrew" >> ~/.bash_profile
echo "export PATH=/usr/local/bin:$PATH" >> ~/.bash_profile
source ~/.bash_profile
```
For this version of Homebrew, "homebrew-science" has been Deprecated. So we no longer need to tap it.

### 3. Install python 2 and 3
For this version of Homebrew,   
```shell
brew install python
```  
will only install python 3. which is same as running 
```shell
brew install python3
```

So what we need to do here for installing both is to run 
```shell
brew install python2 python3
```

After that, you should have both python2 and 3 installed on your Mac.   
You no longer need to link python by your self. Brew will finish the rest for you.
 
```shell
# Check whether Python using homebrew install correctly
which python2  # it should output /usr/local/bin/python2
which python3  # it should output /usr/local/bin/python3

# Check Python versions
python2 --version # it should output Python 2.7.15
python3 --version # it should output Python 3.7.0
```
   According to VAIBHAW SINGH CHANDEL and my personal experience, this version of Homebrew will  
   Install python2 at /usr/local/bin/python2  
   Install python3 at /usr/local/bin/python3  
   and python command will point to /usr/bin/python. This is the python distribution which comes with your OS and not installed by Homebrew.

### 4. Install OpenCV 3 with Python bindings on macOS using Homebrew
This step now is much easier than before. Homebrew has been simplified a lot for this part. You dont need to care about options and bindings. Just run
```shell
brew install opencv
```
And add OpenCV’s site-packages path to global site-packages by 
```shell
#python2
echo /usr/local/opt/opencv/lib/python2.7/site-packages >> /usr/local/lib/python2.7/site-packages/opencv3.pth
#python 3
echo /usr/local/opt/opencv/lib/python3.7/site-packages >> /usr/local/lib/python3.7/site-packages/opencv3.pth
```

Note that there is one thing weird about opencv for python3 about the binding name. You could rename it by using
```shell
cd /usr/local/opt/opencv3/lib/python3.7/site-packages/
mv cv2.cpython-37m-darwin.so cv2.so
```


Test if python/opencv installed successfully
```shell
python2
Python 2.7.15 (default, Jul 23 2018, 21:27:06) 
[GCC 4.2.1 Compatible Apple LLVM 9.1.0 (clang-902.0.39.2)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import cv2
>>> cv2.__version__
'3.4.2'
>>> quit()
```
```shell
$ python3
Python 3.7.0 (default, Jul 23 2018, 20:22:55) 
[Clang 9.1.0 (clang-902.0.39.2)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import cv2
>>> cv2.__version__
'3.4.2'
>>> quit()
```

### 5. Setup your Python virtual environment
follow the steps bellow, you should be able ti set up the virtual env successfully.
```shell
pip install virtualenv virtualenvwrapper
echo "# Virtual Environment Wrapper"
echo "source /usr/local/bin/virtualenvwrapper.sh" >> ~/.bash_profile
source ~/.bash_profile

mkvirtualenv cv-py2 -p python2
mkvirtualenv cv-py3 -p python3
```
I met a problem about setting up opencv for the virtual env. Which is if I run the link from the same "/usr/local/opt/opencv" folder. Only the python 3.7 virtual machine will have opencv successfully. What I did here is to use "/usr/local/opt/opencv@3".Which actually pointing to the same place as "/usr/local/opt/opencv" did.
```shell
cd ~/.virtualenvs/cv-py2/lib/python2.7/site-packages/
ln -s /usr/local/opt/opencv/lib/python2.7/site-packages/cv2.so cv2.so
cd ~/.virtualenvs/cv-py3/lib/python3.7/site-packages/
ln -s /usr/local/opt/opencv@3/lib/python3.7/site-packages/cv2.so cv2.so
```

Test if virtual python/opencv installed successfully
```shell
workon cv-py2
python2

Python 2.7.15 (default, Jul 23 2018, 21:27:06) 
[GCC 4.2.1 Compatible Apple LLVM 9.1.0 (clang-902.0.39.2)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import cv2
>>> cv2.__version__
'3.4.2'
>>> quit()
deactivate
workon cv-py3
python3

Python 3.7.0 (default, Jul 23 2018, 20:22:55) 
[Clang 9.1.0 (clang-902.0.39.2)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import cv2
>>> cv2.__version__
'3.4.2'
>>> quit()
deactivate
```
