# P4 IGNORE
the .p4ignore is a very useful configuration file, whatever files that are listed in the .p4ignore file will not be tracked by perforce. 

Visual Studio, Unreal and Unity all have a million generated files that are re-generatable, fairly big, and hard to keep track of and syncronize, we almost always want to ignore them.

## Two things nees to be done to use p4 ignore properly:

1, Put the corresponding .p4ignore files at the root folder of the game project.

<a href="../vendor/unreal/.p4ignore" download>Unreal</a>

<a href="../vendor/unity/.p4ignore" download>Unity</a>

2, Setup the P4IGNORE [Environment Variable](SystemVariable.md) on your machine if you haven't. 
