# fc

fc: 
fc [-e ename] [-nlr] [first] [last] or fc -s [pat=rep] [cmd]
    
fc is used to list or edit and re-execute commands from the history list.

FIRST and LAST can be numbers specifying the range, or FIRST can be a string, which means the most recent command beginning with that string.
  
-e ENAME selects which editor to use.  Default is FCEDIT, then EDITOR, then vi.
-l means list lines instead of editing.
-n means no linOe numbers listed.
-r means reverse the order of the lines (making it newest listed first).
    
With the `fc -s [pat=rep ...] [command]' format, the command is re-executed after the substitution OLD=NEW is performed.
    
A useful alias to use with this is r='fc -s', so that typing `r cc' runs the last command beginning with `cc' and typing `r' re-executes the last command.
