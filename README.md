# pipes
pipes(), popen(), pclose(), Named pipes:FIFO

-sending messages between two processes using signals.
-created notification events that could be used to provoke a response.
-the information transferred was limited to a signal number.

PIPES

-allow more useful data to be exchanged between processes.

-Generally we attach, or pipe, the output of one process to the input of another.



#include <stdio.h>

FILE *popen(const char *command, const char *open_mode);

int pclose(FILE *stream_to_close);


POPEN()

popen function allows a program to invoke another program as a new process and :

	-either pass data to it 
	
	-or receive data from it.

"command" string is the name of the program to run, together with any parameters. 

"open_mode" must be either “r” or “w” .

If the "open_mode" is "r" , output from the invoked program is made available to the invoking program 
and can be read from the file stream "FILE * " returned by "popen" , using the usual stdio library functions 
for reading (for example, "fread" ).


if "open_mode" is "w" , the program can send data to the invoked command with calls to "fwrite" . 
The invoked program can then read the data on its standard input.


A to popen must specify either "r" or "w".

	-means that we can’t invoke another program and both read and write to it.
	
		-If you want bidirectional communication using pipes, then the normal solution is to use two pipes, 
		 one for data flow in each direction.

	-On failure, popen returns a null pointer.



PCLOSE()

When the process started with "popen" has finished, we can close the file stream associated with it using "pclose" .

The "pclose" call will return only when the process started with "popen" finishes.

If it’s still running when "pclose" is called, the "pclose" call will wait for the process to finish.

The "pclose" call normally returns the exit code of the process whose file stream it is closing.

	-If the invoking process has executed a "wait" statement before calling "pclose" , the "exit status" will be lost and 	"pclose"
	 will return (-1), with "errno" set to "ECHILD" .



THE PIPE CALL 

pipe() function provides a means of passing data between two programs, 

without the overhead of invoking a shell to interpret the requested command.

-It also gives us more control over the reading and writing of data.

#include <unistd.h>

int pipe(int file_descriptor[2]);

-It fills the array with two new file descriptors and are connected in a special way.

	- Any data written to "file_descriptor[1]" can be read back from "file_descriptor[0]" .
	
	- data is processed in a first in, first out basis, usually abbreviated to "FIFO".
	
	- if you write the bytes 1 , 2 , 3 to "file_descriptor[1]" ,
	  reading from "file_descriptor[0]" will produce 1 , 2 , 3 .

- NOTE:: It’s important to realize that these are "file descriptors", not file streams, so we must use the "lower-level" "read" and "write" calls to access the data, rather than "fread" and "fwrite".

Returns::
	- 0 on success.
	
	- (-1) on failure.(sets "errno" to indicate the reason for failure.) 
	
		- "errno"
			- "EMFILE": Too many file descriptors are in use by the process.
			
			- "ENFILE": The system file table is full.
			
			- "EFAULT": The file descriptor is not valid.
