# Setup System Environment Variables

You should setup some of the important environment variables so your system knows what your default configuration is.

1, Press the ```Start``` Button, and search for ```Environment Variable``` find and launch the ```Edit the system envrionment``` option:

<img src="../Assets/EditEnvironmentVarIcon.png">

2, In the ```System Properties``` window, click ```Environment Variables...``` and in the pop up window, click the ```New``` Button. and in the new pop up window, set the ```Variable name``` to ```P4PORT``` and ```Variable value``` to ```10.40.14.107:1666```. Click the ```OK``` button to add the variable. Perfore will try to find this environment variable to use as the port (the adress to connect to the server). You should also add the following envrionment variables:

|  Variable Name | Variable Value |
|----------------|----------------|
| P4PORT         | 10.40.14.25:1666      |
| P4IGNORE       | .p4ignore      |
| P4USER         | Your User Name |

```NOTE, the image shows P4PORT value is 10.40.14.107:1666, that is an old address, use 10.40.14.25:1666 instead```

<img src="../Assets/P4SystemEnv.png">

The ```P4IGNORE``` is ```VERY IMPORTANT```, it tells perforce what files to not track and sync with the server, for both ```Unity``` and ```Unreal Engine```, there are a lot of generated files that are both re-generatable and super elaborate to keep track of and syncronize. 

If you are a programmer, the .p4ignore servers the same purpose as .gitignore. But they work very differently, .gitignore is for the repos, but the .p4ignore is per client, you have specifically setup the ```P4IGNORE``` system varible on each of the machine you want to use the .p4ignore, it will not funcion if the environment variable is missing.


## Setup on Mac 

* fire up a terminal

* open ```.zshrc``` in terminal
```sh
vim ~/.zshrc
```
Note that you can also use ```vscode``` or any other text editor to edit the file, if using ```vscode``` then do:
```sh
code ~/.zshrc
```

* add the following line to the end of ```.zshrc```
```sh
export P4ROOT="localhost:1666"
export P4USER="username"
export P4IGNORE=".p4ignore"
```
save the file, and reload the ```.zshrc```
```sh
source ~/.zshshrc
```

## Setup on Linux Machine

* fire up a terminal

* open ```.bashrc``` in terminal
```
vim ~/.bashrc
```

Note that you can also use ```vscode``` or any other text editor to edit the file, if using ```vscode``` then do:
```sh
code ~/.bashrc
```

* add the following line to the end of ```.bashrc```
```sh
export P4ROOT="localhost:1666"
export P4USER="username"
export P4IGNORE=".p4ignore"
```
save the file, and reload the ```.bashrc```
```sh
source ~/.bashrc
```