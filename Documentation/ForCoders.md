# CLI Commands 

For coders who are more comfortable to use the terminal, here are some userful comands, you can also check the Perforce Visual Client's log to see what button fires what command.

It's important to setup the [environement variables](SystemVariable.md) before you attemp CLI command.    

# Streams

Streams are similar to branches in Git, but offers more control over how they branch out, sync, and merge. to understand more about stream: [Official Documentation of Streams](https://www.perforce.com/manuals/p4guide/Content/P4Guide/chapter.streams.html)

### Create the Mainline Stream
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


### Create the Development Stream
A development stream is a stream that is branched out(is the child of) from the mainline stream. It is used for development of new features, and once the feature is complete, the development stream can be merged(sync) with the mainline stream.

to create a development stream, we need to specify the parent stream with ```-P``` option:
```sh
p4 stream -t development -P //[depot_name]/main //[depot_name]/feature_01
```
this will create a new stream named ```feature_01``` under the depot we just created, and the stream type is ```development```, and the parent stream is ```//[depot_name]/main```.

### Create the Sparse Stream
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
# Workspace

a workspace is a configuration of what depot and what part of it is mapped to a local machine. it defines some what like a local repository.

it is also called a client in the pd CLI command.

it has 2 major parts:

* Client Mapping (View)

    defines what part of the depot is mapped to the client workspace.

* Root
    the director to work with, stores all the files mapped, as well as the place you can add new file or make changes to existing files.

## check existing workspaces:
```sh
p4 clients
```
## delete a workspace:
```sh
p4 client -d [client_name]
```

## make a workspace:
```sh
p4 client [client_name]
```
A client file named ```[client_name].p4client``` will be created, and the editor defined in the P4EDITOR environment variable will be used to edit the client configuration file.

the important part of the client configuration file is the ```Root``` and ```View``` fields:

```
Root: [local_root_directory_to_store_depot_files]
View: [depot_path] [local_path]
```

the ```[local_root_directory]``` is the local directory that is mapped to the depot.

example:
```
Root: C:\Path\To\Local\Root\Dir
View: //[depot_name]/[stream_name]/... //[client_name]/[stream_name]/...
```
this creates a workspace(client) that will map the [stream_name] of the depot named [depot_name] to the local directory ```C:\Path\To\Local\Root\Dir\[stream_name]```.

## set the workspace in cli
on windows:
```cmd
$env:P4CLIENT = "[client_name]"
```
on mac/linux:
```sh
export P4CLIENT="[client_name]"
```
to check the current new changes:
first ```cd``` to the root of the workspace:
then:
```sh
p4 reconcile -n
```
this list all the new changes in the workspace, ```-n``` means do not make any action, just to list them.
this is similar to ```git status```.

to add the changes to change list:
```sh
p4 reconcile
```
this add the new changes to the default change list.

to add to a specific change list:
```sh
p4 reconcile -c [change_list_number]
```
you can also create a new change list:
```sh
p4 change
```
this will create a new change list, and the editor defined in the P4EDITOR environment variable will be used to edit the change list description.

you can then adde the new changes to the new change list:
```sh
p4 reconcile -c [new_change_list_number]
```
to see what is in the change lists:
```sh
p4 opened
```
to submit the change list:
```sh
p4 submit -c [change_list_number]
```
if you are only doing:
```sh
p4 submit
```
it will submit the changes in the default change list.
there is no way to submit all change list in one go. onless to use a shell script.

to check all pending change lists:
```sh
p4 changes -s pending
```
to sync changes(update all revisions)
```sh
p4 sync
```