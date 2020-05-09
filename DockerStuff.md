# Docker Stuff


## CMD and Entrypoint

What are they :
* CMD - commands that are run at container startup. Over ridden by any commands that are set at Docker run time 
* ENTRYPOINT - A command that is ALWAYS run at container startup.

Combine them, use entry point to run the container process, use CMD to provide default values if needed. 