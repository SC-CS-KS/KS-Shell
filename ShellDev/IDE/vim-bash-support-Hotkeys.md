# vim + bash-support Hotkeys

-- bash help ------------------------------------------
     \hb     Displays the Bash manual         (n, i)
     \hh     Displays help for the builtin    (n, i)
             under the cursor (Bash help).
             The tab expansion is active.
     \hm     displays the manual for the      (n, i)
             Bash command under the cursor
             The tab expansion is active.
     \hbs    Displays the Vim help page for   (n, i)
             this plugin.

  -- Comments -------------------------------------------
  [n]\cl     line end comment                 (n, i, v)
  [n]\cj     adjust end-of-line comments      (n, i, v)
     \cs     set end-of-line comment column   (n)
     \cfr    frame comment                    (n, i)
     \cfu    function description             (n, i)
     \ch     file header                      (n, i)
     \ckb    keyword comment BUG              (n, i)
     \ckt    keyword comment TODO             (n, i)
     \ckr    keyword comment TRICKY           (n, i)
     \ckw    keyword comment WARNING          (n, i)
     \ckn    keyword comment New_Keyword      (n, i)
  [n]\cc     toggle comment                   (n, i, v)
     \cd     date                             (n, i, v)
     \ct     date & time                      (n, i, v)
     \ce     echo "..."                       (n, i)
     \cr     remove echo "..."                (n, i)
     \css    script sections (tab completion) (n, i)
     \ckc    keyword comments(tab completion) (n, i)
     \cv     vim modeline                     (n, i)

  -- Statements ------------------------------------------
     \sc     case in ... esac                 (n, i)
     \sei    elif then                        (n, i)
     \sf     for in do done                   (n, i, v)
     \sfo    for ((...)) do done              (n, i, v)
     \si     if then fi                       (n, i, v)
     \sie    if then else fi                  (n, i, v)
     \ss     select in do done                (n, i, v)
     \su     until do done                    (n, i, v)
     \sw     while do done                    (n, i, v)
     \sfu    function                         (n, i, v)
     \se     echo -e "..."                    (n, i, v)
     \sp     printf "..."                     (n, i, v)
     \sa     array element ${.[.]}            (n, i, v)
     \saa    array elements ${.[@]}           (n, i, v)
     \sa1    array elements, 1 word ${.[*]}   (n, i, v)
     \ssa    subarray ${.[@]::}               (n, i, v)
     \san    no. of array elements ${#.[@]}   (n, i, v)
     \sai    list of arr. indices ${!.[*]}    (n, i, v)

  -- Snippets --------------------------------------------
     \nr     read code snippet                (n, i)
     \nw     write code snippet               (n, v, i)
     \ne     edit code snippet                (n, i)

     \ntl    edit local templates             (n, i)
     \ntg    edit global templates            (n, i)
     \ntr    reread the templates             (n, i)
     \nts    switch template style            (n, i)

  -- Test ------------------------------------------------
     \t1     unary operator, 1 arg.: \verb'[ -  ]'  (n, i)
     \t2     binary operator, 2 arg: \verb'[  -  ]' (n, i)

  -- Regular Expression ----------------------------------
     \xm     [[  =~  ]]                      (n, i)

  -- POSIX Character Classes -----------------------------
     \pan    [:alnum:]                        (n, i)
     \pal    [:alpha:]                        (n, i)
     \pas    [:ascii:]                        (n, i)
     \pb     [:blank:]                        (n, i)
     \pc     [:cntrl:]                        (n, i)
     \pd     [:digit:]                        (n, i)
     \pg     [:graph:]                        (n, i)
     \pl     [:lower:]                        (n, i)
     \ppr    [:print:]                        (n, i)
     \ppu    [:punct:]                        (n, i)
     \ps     [:space:]                        (n, i)
     \pu     [:upper:]                        (n, i)
     \pw     [:word:]                         (n, i)
     \px     [:xdigit:]                       (n, i)

  -- Run -------------------------------------------------
     \rr     update file, run script          (n, i)
     \ra     set script cmd. line arguments   (n, i)
     \rba    set Bash cmd. line arguments     (n, i)
     \rc     update file, check syntax        (n, i)
     \rco    syntax check options             (n, i)
     \rd     start debugger                   (n, i)
     \re     make script executable           (n, i)
     \rh     hardcopy buffer to FILENAME.ps   (n, i)
     \rs     settings and hot keys            (n, i)
     \rt     set xterm size                   (n, i)
     \ro     change output destination        (n, i)

  -- Load / Unload bash Support --------------------------
     \lbs    Load Bash Support                (n, GUI only)
     \ubs    Unload Bash Support              (n, GUI only)
