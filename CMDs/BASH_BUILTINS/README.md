# BASH_BUILTINS

```md
  bash,  :, ., [, alias, bg, bind, break, builtin, cd, command, compgen, complete, continue, declare, dirs, disown, echo, enable, eval, exec,
       exit, export, fc, fg, getopts, hash, help, history, jobs, kill, let, local, logout, popd, printf, pushd, pwd, read, readonly, return,  set,
       shift, shopt, source, suspend, test, times, trap, type, typeset, ulimit, umask, unalias, unset, wait - bash built-in commands, see bash(1)
```

Table of Contents
=================

   * [BASH_BUILTINS](#bash_builtins)
      * [alias unalias](#alias-unalias)
      * [builtin](#builtin)
      * [command](#command)
      * [enable](#enable)
      * [fc](#fc)
      * [set](#set)
      * [source](#source)
      * [trap](#trap)
      * [umask](#umask)
      * [wait](#wait)

## alias unalias
```md
当命令被读取时，Shell会展开别名定义，别名主要用于交互式Shell。
	如：alias 'rm=rm -i' 指删除时，需要确认。
	Shell不会做递归的别名展开，所有此定义是合法的。
```
## builtin
```md
Run a shell builtin.  
builtin [shell-builtin [arg ...]]   
This is useful when you wish to rename a shell builtin to be a function, 
but need the functionality of the builtin within the function itself.
```
## command
```md
NAME
       command - execute a simple command
SYNOPSIS
       command [-p] command_name [argument ...]
       command [ -v | -V ] command_name
PROLOG
       This manual page is part of the POSIX Programmer’s Manual.  The Linux implementation of this interface may differ
       (consult the corresponding Linux manual page for details of Linux behavior), or the interface may not  be  imple-
       mented on Linux.
```

## enable
```md
Enable and disable builtin shell commands.  
This allows you to use a disk command(磁盘操作命令) which has the same name as a shell builtin without specifying a full pathname. 
enable [-pnds] [-a] [-f filename] [name ...]
Options
	If no non-option names are given, or the -p option is supplied, a list of builtins is printed.  
	-n
		The -n option displays a list of all disabled builtins.
```

## fc
```md
fc is used to list or edit and re-execute commands from the history list.
fc [-e ename] [-nlr] [first] [last] or fc -s [pat=rep] [cmd]
```

## set
```md
set [--abefhkmnptuvxBCHP] [-o option] [arg ...]
Options
	        -a  Mark variables which are modified or created for export.
	        -b  Notify of job termination immediately.
	        -e  Exit immediately if a command exits with a non-zero status.
	        -f  Disable file name generation (globbing).
	        -h  Remember the location of commands as they are looked up.
	        -k  All assignment arguments are placed in the environment for a command, not just those that precede the command name.
	        -m  Job control is enabled.
	        -n  Read commands but do not execute them.
		-o  option-name
		Set the variable corresponding to option-name:
			                allexport    same as -a
			                braceexpand  same as -B
			                emacs        use an emacs-style line editing interface
			                errexit      same as -e
			                errtrace     same as -E
			                functrace    same as -T
			                hashall      same as -h
			                histexpand   same as -H
			                history      enable command history
			                ignoreeof    the shell will not exit upon reading EOF
			                interactive-comments
				 allow comments to appear in interactive commands
			                keyword      same as -k
			                monitor      same as -m
			                noclobber    same as -C
			                noexec       same as -n
			                noglob       same as -f
			                nolog        currently accepted but ignored
			                notify       same as -b
			                nounset      same as -u
			                onecmd       same as -t
			                physical     same as -P
			                pipefail     the return value of a pipeline is the status of
                             the last command to exit with a non-zero status,
                             or zero if no command exited with a non-zero status
			                posix        change the behavior of bash where the default
                             operation differs from the 1003.2 standard to
                             match the standard
			                privileged   same as -p
			                verbose      same as -v
			                vi           use a vi-style line editing interface
			                xtrace       same as -x
	        -p  Turned on whenever the real and effective user ids do not match.
            Disables processing of the $ENV file and importing of shell
            functions.  Turning this option off causes the effective uid and
            gid to be set to the real uid and gid.
	        -t  Exit after reading and executing one command.
	        -u  Treat unset variables as an error when substituting.
	        -v  Print shell input lines as they are read.
	        -x  Print commands and their arguments as they are executed.
	        -B  the shell will perform brace expansion
	        -C  If set, disallow(驳回，不允许) existing regular files to be overwritten(覆盖) by redirection(重定向) of output.
	        -E  If set, the ERR trap is inherited by shell functions.
	        -H  Enable ! style history substitution.  This flag is on by default when the shell is interactive.
	        -P  If set, do not follow symbolic links when executing commands such as cd which change the current directory.
	        -T  If set, the DEBUG trap is inherited by shell functions.
	        -   Assign any remaining arguments to the positional parameters.
		            The -x and -v options are turned off.
```

## source
```md
Read and execute commands from FILENAME and return.  
	注：该命令通常用命令“.”来替代。如：source .bash_rc 与 . .bash_rc 是等效的。
	source命令(从 C Shell 而来)是bash shell的内置命令。点命令，(从Bourne Shell而来)是source的另一名称。
source filename [arguments]
```

## trap
```md
The command ARG is to be read and executed when the shell receives signal(s) SIGNAL_SPEC.  
	trap [-lp] [arg signal_spec ...]
```

## umask
```md
The user file-creation mask is set to MODE. 
	umask [-p] [-S] [mode]
Options
	If MODE is omitted, or if `-S' is supplied, the current value of the mask is printed. 
	The `-S' option makes the output symbolic; otherwise an octal number is output.
	If `-p' is supplied, and MODE is omitted, the output is in a form that may be used as input.  
	If MODE begins with a digit, it is interpreted as an octal number, otherwise it is a symbolic mode string like that accepted by chmod(1).
```

## wait
```md
等待进程执行完后返回
	该指令常用于shell脚本编程中，待指定的指令执行完成后，才会继续执行后面的任务。
	该指令等待作业时，在作业标识号前必须添加备份号"%"。
		$ wait %1               #等待作业号为1的作业完成 
wait [n]
	If N is not given, all currently active child processes are waited for,and the return code is zero.  
	N is a process ID; if it is not given, all child processes of the shell are waited for.
```