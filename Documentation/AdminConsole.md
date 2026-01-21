# Use Console To Administrate the Server

To use the console, the system environment variable need to be set:
* [Setup the environment variable](SystemVariable.md)

## Users

#### Add User
open terminal, and type:
```sh
p4 user -f [user_name] 
```
This add a user with the name user_name, the -f means we force add the user without give a password, doing so will allow us to add a user first, and they can setup their password later.

for example:
```sh
p4 user -f adam
```

A text edior will pop up for you to define more details about the user, you can simply save and close the text editor, or you can adjust their email and user name.

To adjust an existing user, it's the same as adding one:
```sh
p4 user -f [user_name]
```


#### Remove User:
```sh
p4 user -d [user_name] 
```

#### check existing users
```sh
p4 users
```

#### Set user permission
* open the protect file:
```sh
p4d protect
```
* add the user permission by adding a new line under the ```protections``` section.
The following line add user_name to have super access over everything:
```sh
super user [user_name] * //...
```
The following line add user_name write access over the depotName depot
```sh
write user [user_name] * //depotName/...
```
* save and close the file

* check user permissions:
```sh
p4 protects -u [user_name]
```
## Groups
### Add a Group:
```
p4 group [group_name]

```
you can then add users to the group under the ```Users``` section
you can edit an existing group the with the same command you added the group.

### Set Group Permission 

* Open the p4 protect:
```sh
p4d protect
```
* Add the group permission under the ```permissions``` section.
The following line gives group_name ```admin``` access to the entire server
```sh
admin group [group_name] * //...
```
The following line give group_name ```write``` access to the DepotName depot
```sh
write group [group_name] * //DepotName/...
```
* save and close the file

* check permission of a group:
```sh
p4 protects -g [group_name]
```

## Depot (Project/Repository)
A depot is a project in Perforce, it is the top-level container for all assets and source code related to a specific project. It is the same as a git repository.

### Check Existing Depots
```sh
p4 depots
```
### Remove a Depot:
```sh
p4 depot -d [depot_name]
```

### Create a Depot:
to create depot:
```sh
p4 depot -t stream [depot_name] 
```
This will create a new stream depot with the name specified by [deportName], and the type of depot is ```stream```.

if you prefer the legacy depot, use:
```sh
p4 depot -t local [depot_name]
```
the editor defined in the P4EDITOR environment variable will be used to edit the depot configuration file, so it is reommended to set the P4EDITOR to a more coder friendly editor like ```vscode```, etc.

the only thing to set is the Map field, this is the relative path the depot will be place under the server root directory. Usually as: 
```
Map: [depot_name]/...
```
In situations where mutiple depot needs to be organized in a folder hierachy, we can specify a subfolder as the depot name, for example:
```
Map: [depot_category_name]/[depot_name]/... 
```
the ```...``` is a wildcard character that matches all characters in the path. Means all files and subfolders are all be placed in here.

### Add Streams to the Depot

Streams are similar to branches in Git, but offers more control over how they branch out, sync, and merge. to understand more about stream: [Official Documentation of Streams](https://www.perforce.com/manuals/p4guide/Content/P4Guide/chapter.streams.html)

## Create the Mainline Stream
A stream typed depot need to have a mainline stream, just like a git repository need at least a master brach, we can add it by:
```sh
p4 stream -t [stream_type] -P [parent_stream(optional)] [stream_path]
```
this will create a new stream, and the editor defined in the P4EDITOR environment variable will be used to edit the stream configuration file.

an example to add a stream named ```main``` to the depot we just created:
```sh
p4 stream -t mainline //[depot_name]/main 
```
this will create a new stream named ```main``` under the depot we just created, and the stream type is ```mainline```, the mainline stream is like the master branch in Git, it is the main stream that all changes are synced to. It has no parent stream.


## Create the Development Stream
A development stream is a stream that is branched out(is the child of) from the mainline stream. It is used for development of new features, and once the feature is complete, the development stream can be merged(sync) with the mainline stream.

to create a development stream, we need to specify the parent stream with ```-P``` option:
```sh
p4 stream -t development -P //[depot_name]/main //[depot_name]/feature_01
```
this will create a new stream named ```feature_01``` under the depot we just created, and the stream type is ```development```, and the parent stream is ```//[depot_name]/main```.

## Create the Sparse Stream
A spase stream is a lightweight stream that usually used for specific tasks needs to be done for a parent stream. Usually an individual can create a new sparse stream for a quick bug fix or small feature development, if the task grows bigger, the sparse stream can be converted to a no-sparse stream like a development stream.

sparse streams allow you to work with a new stream quickly. If your organization has a "branch per bug" workflow, sparse streams might accelerate productivity.

The two types of sparse streams are:
* sparsedev

    the purpose of a sparsedev is for a specific tasks of a development stream.
    A sparsedev stream avoids a lengthy populate operation from the mainline stream, and is well suited for developing a feature involving a relatively small number of files.


* sparserel

    the purpose of a sparserel is for a specific tasks of a release stream, good for hot fixes.


to make a sparse stream, we need to specify the parent stream with ```-P``` option:

```sh
p4 stream -t sparsedev -P //[depot_name]/feature_01 //[depot_name]/feature_01_part_01_by_user_01
```