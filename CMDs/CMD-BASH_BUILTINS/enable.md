# enable

enable: 
enable [-pnds] [-a] [-f filename] [name ...]



Enable and disable builtin shell commands.  
This allows you to use a disk command(磁盘操作命令) which has the same name as a shell builtin without specifying a full pathname. 



If -n is used, the NAMEs become disabled; otherwise NAMEs are enabled.  
For example,to use the `test' found in $PATH instead of the shell builtin version, type `enable -n test'.  

On systems supporting dynamic loading, the -f option may be used to load new builtins from the shared object FILENAME.  

The -d option will delete a builtin previously loaded with -f.  

If no non-option names are given, or the -p option is supplied, a list of builtins is printed.  

The -a option means to print every builtin with an indication of whether or not it is enabled.  

The -s option restricts the output to the POSIX.2 `special' builtins.  

The -n option displays a list of all disabled builtins.

