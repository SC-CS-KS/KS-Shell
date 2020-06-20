# Process Substitution and Pipes

Process Substitution and Pipes
Posted on March 22, 2012
Command substitution is a widely used feature of the Bash and Korn shells, 
allowing the output of one command to be captured and used in another. 
Like this:
bash-4.2$ echo “Backup started at $(date)”
Backup started at Fri Mar 16 15:35:14 GMT 2012

Command substitution is not to be confused with that less well known (and, if we’re honest, less useful) shell feature, process substitution. Despite being rarely used, process substitution is worth knowing about, if only because it illuminates other fundamental unix features – the shell, sub processes, named and unnamed pipes.

This post discusses process substitution, command substitution and the vertical bar (|). 
Three very different shell features, but all making use of unnamed pipes, and so not as different as they first appear.
       The examples are from Linux but also work on Solaris 10 and, due to the ubiquity【普遍存在】 of pipes, are likely to work on other unixes too.

PROCESS SUBSTITUTION
Process substitution is a command syntax allowing the input/output of one command to be connected to the input/output of several other commands. But first, a quick word about pipes. Consider a straightforward pipe like this one:

bash-4.2$ ls -l | sort -n -k 5

It produces a list of files sorted by size. The output of “ls” is connected to the input of “sort”. This kind of pipe, represented by a vertical bar (|), connects just one command to exactly one other command. Process substitution gets around that limitation, allowing “one to many” connections between commands.

Process Substitution Example (Input)
For example, let’s connect the output of “ls” and the output of “df” to the input of that sort. Use the Process substitution input syntax, “<()".

bash-4.2$ sort -n -k 5 <(ls -l) <(df -k)

The "ls” and the “df” now both execute and their outputs are both fed into the input of “sort“. 
(NB the output produced is silly, but this is just a demo of the command).

COMMANDS AMENABLE TO PROCESS SUBSTITUTION
Process substitution is usually used in conjunction with a command that can take multiple file names as arguments.
Such as sort, which takes a list of files on the command line. 
In the above example, the first “file” argument to sort is “<(ls -l)“, which represents the output of “ls -l“. 
The second file argument to sort is “<(df -k)“, which represents the output of the “df -k” command.

That was “input” process substitution, using the “input” process substitution syntax syntax “<()". 
The converse process is "output" process substitution, and uses the syntax ">()”. 
Output process substitution is best demonstrated with a command that works with more than one output file. 
There are a few to choose from but the most appropriate is probably tee.

PROCESS SUBSTITUTION EXAMPLE (OUTPUT)
Tee‘s function is to replicate it’s standard output into every file that you name on the command line. 
It is therefore well suited to work with output process substitution. 
That is, we can use tee to connect the output of one command to the inputs of several others.

Eg. connect the output of one “ls” to each of the inputs of two greps:
bash-4.2$ ls | tee >(grep jpg > list.jpg) >(grep pdf > list.pdf)

A file “list.pdf” is produced containing a list of all pdf files in the current directory. While “list.jpg” contains the files of type jpg.

The above command uses the output command substitution syntax >(). 
The expression “>(grep jpg > list.jpg)” is used as the first of tee’s output files, and “>(grep pdf > list.pdf)” the second. 
The complete ls output is thus fed into both greps, producing the files described.

USES OF PROCESS SUBSTITUTION
We could go on to produce more elaborate examples of process substitution, perhaps substituting both input and output in the same command, nesting the syntax, using longer pipelines and so forth. I am not going to do that, for two reasons:

1. Process substitution is not awfully useful. Anything it can do is better done with simpler commands.

2. Process substitution syntax is excessive. If you want your scripts to be readable and maintainable, don’t pepper them with idioms like “ls | tee >(grep jpg > list.jpg) >(grep pdf > list.pdf)”, or worse. A script written in that way would quickly become unmaintainable, even by its original author.

However, Process substitution is worth understanding because of the lights it sheds on processes and pipes.

HOW A UNIX PIPE WORKS
In the shell, pipes come in two flavours: named and unnamed. The example given at the start of this article was an unnamed pipe. Here it is again.

bash-4.2$ ls -l | sort -n -k 5

When the above command is run, the output of “ls” is connected to the input of “sort” via the pipe. The kernel runs the ls command and sends its output into a small area of memory called a FIFO buffer (first in first out). Meanwhile the input of the sort is connected to the other end of the FIFO. The sort process then goes to sleep while waiting for the ls output to appear in the pipe. When the ls output appears, the sort will wake up and begin to process it. If the FIFO fills up then the ls will go to sleep, and will only re-awaken when there is room in the pipe to push more data in.

SLOW MOTION EXAMPLE – UNNAMED PIPE
To see this happening, we need a slow pipeline. How about this:

bash-4.2$ sleep 3600 | cat &
[1] 3080

It does nothing meaningful, just sets up the pipe, waits for an hour then exits. Check it in another window:

bash-4.2$ ps -elf | grep 3080
0 S james 3080 2049 0 80 0 – 1060 pipe_w 17:22 pts/2 00:00:00 cat

There is the cat, waiting patiently. The “S” in the second column and the flag “pipe_w” shows that the cat process is “sleeping on the pipe”. We can look at the pipe inode too:

bash-4.2$ ls -li /proc/3080/fd
total 0
842398 lr-x——. 1 james james 64 Mar 16 17:29 0 -> pipe:[809936]
842399 lrwx——. 1 james james 64 Mar 16 17:29 1 -> /dev/pts/2
810251 lrwx——. 1 james james 64 Mar 16 17:23 2 -> /dev/pts/2

The standard input of the cat (process 3080) is associated with a pipe inode, inode number 809936. All unix pipes (and all files) are associated with a inode. This inode is part of the /proc file system. (Ignore the other inode numbers on the left. They just correspond to the soft links 0, 1 and 2).

Since the pipe is “unnamed”, it has no associated name in the file system. The find command (run while cat is still sleeping) verifies that, by finding no file with inode number 809936

bash-4.2$ sudo find /proc -iname 809936
bash-4.2$

That’s he end of the test, so just to kill those outstanding processes:

bash-4.2$ kill %1

SLOW MOTION EXAMPLE – NAMED PIPE
Once it has been established, a named pipe works in exactly the same way as unnamed pipe. But it has something the unnamed pipe doesn’t: a name in the file system. To make a unnamed pipe (aka a fifo), use the mkfifo command:

bash-4.2$ cd /tmp
bash-4.2$ mkfifo testfifo

The following ls command shows a “p” on the left, indicating a special device of type “pipe”.

bash-4.2$ ls -l testfifo
prw-rw-r–. 1 james james 0 Mar 20 11:21 testfifo

Now to repeat the above test. Again, using a “sleep” command will slow things down. Direct the output of sleep into the pipe as follows. (Sleep doesn’t make any output, it is just useful for this demonstration).

bash-4.2$ sleep 3600 > testfifo &
[1] 2775

Now set up a “cat” command to read the pipe output, completing the pipeline.

bash-4.2$ cat testfifo &
[2] 2785

As before, check to see what cat is doing. As expected, it is sleeping on the pipe:

bash-4.2$ ps -elf | grep cat
0 S james 2785 2123 0 80 0 – 1060 pipe_w 11:58 pts/0 00:00:00 cat testfifo

Only this time, the pipe has a name, viz. /tmp/testfifo. Have a look at the file descriptors belonging to the cat process, and there it is:

bash-4.2$ ls -li /proc/2785/fd
total 0
253425 lrwx——. 1 james james 64 Mar 20 12:04 0 -> /dev/pts/0
253426 lrwx——. 1 james james 64 Mar 20 12:04 1 -> /dev/pts/0
247244 lrwx——. 1 james james 64 Mar 20 12:02 2 -> /dev/pts/0
253427 lr-x——. 1 james james 64 Mar 20 12:04 3 -> /tmp/testfifo

This time, file descriptor of interest is numbered 3 and the soft link points not just to an inode, but to the associated name – /tmp/testfifo. lsof will provide more information:

bash-4.2$ ls -li /tmp/testfifo
22698 prw-rw-r–. 1 james james 0 Mar 20 11:38 /tmp/testfifo
bash-4.2$ lsof | grep testfifo
sleep 2775 james 1u FIFO 8,5 0t0 22698 /tmp/testfifo
cat 2785 james 3r FIFO 8,5 0t0 22698 /tmp/testfifo

As expected, the fifo /tmp/testfifo is currently open for reading and writing on the standard output of sleep (as evinced by the “1u” in the FD column of lsof. Meanwhile the cat process has the fifo open on file descriptor 3 for reading only. Looking at the pipe, it almost seems that the cat should be reading from file descriptor 1, its standard input. Not so. Recall the name of the pipe was given to cat as an argument, not as a stream to standard in. Cat therefore opened the pipe using the first free file descriptor, which was number 3.

NAMED PIPE AVAILABLE TO OTHER PROCESSES
While the above processes are sleeping, the named pipe is still available for other input. Because it has a name in the file system, it can be accessed by other processes. Eg. Send the output of ls into it, and the file listing appears in the shell:

bash-4.2$ ls > testfifo
file1
file2
…

The cat woke up briefly, printed the ls output, then slept again, waiting for more output from the pipe. The two original processes are still there:

bash-4.2$ ps -elf | egrep “cat|sleep”
0 S james 2775 2123 0 80 0 – 1055 hrtime 12:23 pts/0 00:00:00 sleep 1000
0 S james 2785 2123 0 80 0 – 1060 pipe_w 12:23 pts/0 00:00:00 cat testfifo

Cleaning Up
Kill those test processes. And the named pipe will continue to exist even through a reboot, unless it is removed now:

bash-4.2$ kill %1
bash-4.2$ rm /tmp/testfifo

PROCESS SUBSTITUTION USES UNNAMED PIPES
Just as the above tests showed the bar (|) using unnamed pipes, so process substitution can be seen using them, if things are slowed down enough. The following example makes no practical sense, it is just useful as a demonstration.

bash-4.2$ sort -n -k 5 <(sleep 3600) <(ls -l) &
[1] 3061

Use ps to check the sort process (pid 3061). The above command line can be seen after the sleep and ls commands have been substituted with /dev/fd/63 and /def/fd/62. These are unnamed pipes receiving input from the sleep and ls commands.

ash-4.2$ ps -ef | grep 3061
james 3061 1987 0 18:02 pts/1 00:00:00 sort -n -k 5 /dev/fd/63 /dev/fd/62

More correctly, they are soft links pointing to unnamed pipes that the sort process has open for reading. Checking the sort process under /proc, the unnamed pipes are associated with inodes 538039 and 538040.

bash-4.2$ ls -l /proc/3061/fd
total 0
lrwx------. 1 james james 64 Mar 20 18:05 0 -> /dev/pts/1
lrwx——. 1 james james 64 Mar 20 18:05 1 -> /dev/pts/1
lrwx——. 1 james james 64 Mar 20 18:02 2 -> /dev/pts/1
lr-x——. 1 james james 64 Mar 20 18:05 3 -> pipe:[538039]
lr-x——. 1 james james 64 Mar 20 18:02 62 -> pipe:[538040]
lr-x——. 1 james james 64 Mar 20 18:02 63 -> pipe:[538039]

The kernel has taken two inodes from the pipe device (/proc) and used them to create the necessary unnamed pipes. A sub shell (pid

EVEN COMMAND SUBSTITUTION USES UNNAMED PIPES
The test above, showing the vertical bar (|) using unnamed pipes, can be repeated for command substitution:

bash-4.2$ /bin/echo “That was a nice sleep $(sleep 3600)” &
[1] 3132

Again, the command is pointless but useful as a demonstration. Checking the processes involved:

bash-4.2$ ps -elf | grep 3132
james 3132 1987 0 18:28 pts/1 00:00:00 bash
james 3133 3132 0 18:28 pts/1 00:00:00 sleep 3600

Process 3132 is a bash shell sub process spawned to handle the sleep command. Looking into the file descriptors of both processes:

bash-4.2$ ls -l /proc/3133/fd
total 0
lrwx——. 1 james james 64 Mar 20 18:28 0 -> /dev/pts/1
l-wx——. 1 james james 64 Mar 20 18:28 1 -> pipe:[585486]
lrwx——. 1 james james 64 Mar 20 18:28 2 -> /dev/pts/1

bash-4.2$ ls -l /proc/3132/fd
total 0
lrwx——. 1 james james 64 Mar 20 18:28 0 -> /dev/pts/1
lrwx——. 1 james james 64 Mar 20 18:28 1 -> /dev/pts/1
lrwx——. 1 james james 64 Mar 20 18:28 2 -> /dev/pts/1
lrwx——. 1 james james 64 Mar 20 18:28 255 -> /dev/pts/1
lr-x——. 1 james james 64 Mar 20 18:28 3 -> pipe:[585486]

…shows that they are connected by an unnamed pipe with inode 585486. The kernel has taken this inode from the pipe device /proc to handle communication between the processes.

CONCLUSION
Process substitution, command substitution and the vertical bar (|) are all implemented using unnamed pipes. It is sometimes written that command substitution uses named pipes. While that was true in the past (eg. 1997) it no longer seems to be so at the time of writing (March 2012).

Process substitution is used very rarely, command substitution is common, especially within scripts, and the most widely used of all is the vertical bar (|), perhaps the most useful item in the whole of Unix.

Named pipes are infrequently used, sometimes appearing where a command can read or write only to a file, and the user therefore substitutes the name of the pipe for the file. Or in the making of database dumps or other communication between applications and the OS.
