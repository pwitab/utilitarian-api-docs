# Execution Environments.

When reading a meter in Utilitarian (Pull Architecture) we provide a number of different 
execution environments. These environments provide a number of methods to call on the 
meter that has been loaded into the environment.

AmrTask are saved with knowledge of the execution environment it is supposed to run in.

We are having a concept of generic environments and specific environments. A generic 
environment is designed so that most meters using the same technology/protocol can use 
the environment. But if a meter type with the same protocol does a action in a slightly 
different way than the normal a special specific environment is made for that meter at 
integration time to facilitate the different functionality  