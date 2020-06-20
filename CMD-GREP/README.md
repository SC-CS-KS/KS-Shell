# 30.GREP

grep (global search regular expression(RE) and print out the line）
全面搜索正则表达式并把行打印出来)是一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹配的行打印出来。

grep的工作方式是这样的，它在一个或多个文件中搜索字符串模板。如果模板包括空格，则必须被引用，模板后的所有字符串被看作文件名。
搜索的结果被送到屏幕，不影响原文件内容。

grep可用于shell脚本，因为grep通过返回一个状态值来说明搜索的状态。
如果模板搜索成功，则返回0，如果搜索不成功，则返回1，如果搜索的文件不存在，则返回2。
我们利用这些返回值就可进行一些自动化的文本处理工作。

语法: 
grep [options] PATTERN [FILE...]
grep用以在file内文中比对相对应的部分，或是当没有指定档案时，由标准输入中去比对。
在预设的情况下，grep会将符合样式的那一行列出。
此外，还有两个程序是grep的变化型，egrep及fgrep。其中egrep就等同于grep -E ，fgrep等同于grep -F。
-----------------------------------------------------------------------------------------------------------------
NAME
       grep, egrep, fgrep - print lines matching a pattern

SYNOPSIS
       grep [OPTIONS] PATTERN [FILE...]
       grep [OPTIONS] [-e PATTERN | -f FILE] [FILE...]

DESCRIPTION
       grep searches the named input FILEs (or standard input if no files are named, or if a single hyphen-minus (-) is given as
       file name) for lines containing a match to the given PATTERN.  By default, grep prints the matching lines.

       In addition, two variant programs egrep and fgrep are available.  egrep is the same as grep -E.  fgrep  is  the  same  as
       grep -F.  Direct invocation as either egrep or fgrep is deprecated, but is provided to allow historical applications that
       rely on them to run unmodified.

OPTIONS
   Generic Program Information
       --help Print a usage message briefly summarizing these command-line options and the bug-reporting address, then exit.

       -V, --version
              Print the version number of grep to the standard output stream.  This version number should be included in all bug
              reports (see below).

REGULAR EXPRESSIONS
       A  regular  expression  is a pattern that describes a set of strings.  Regular expressions are constructed analogously to
       arithmetic expressions, by using various operators to combine smaller expressions.

       grep understands two different versions of regular expression syntax: "basic" and "extended."  In GNU grep, there  is  no
       difference  in available functionality using either syntax.  In other implementations, basic regular expressions are less
       powerful.  The following description applies to extended regular expressions; differences for basic  regular  expressions
       are summarized afterwards.

       The  fundamental  building  blocks are the regular expressions that match a single character.  Most characters, including
       all letters and digits, are regular expressions that match themselves.  Any meta-character with special  meaning  may  be
       quoted by preceding it with a backslash.

       The period . matches any single character.

   Character Classes and Bracket Expressions
       A  bracket  expression is a list of characters enclosed by [ and ].  It matches any single character in that list; if the
       first character of the list is the caret ^ then it matches any character not in  the  list.   For  example,  the  regular
       expression [0123456789] matches any single digit.

       Within  a bracket expression, a range expression consists of two characters separated by a hyphen.  It matches any single
       character that sorts between the two characters, inclusive, using the locale's collating sequence and character set.  For
       example,  in  the default C locale, [a-d] is equivalent to [abcd].  Many locales sort characters in dictionary order, and
       in these locales [a-d] is typically not equivalent to [abcd]; it might be  equivalent  to  [aBbCcDd],  for  example.   To
       obtain  the traditional interpretation of bracket expressions, you can use the C locale by setting the LC_ALL environment
       variable to the value C.

       Finally, certain named classes of characters are predefined within bracket expressions, as follows.  Their names are self
       explanatory,  and  they  are  [:alnum:],  [:alpha:],  [:cntrl:],  [:digit:],  [:graph:], [:lower:], [:print:], [:punct:],
       [:space:], [:upper:], and [:xdigit:].  For example, [[:alnum:]] means [0-9A-Za-z], except the latter  form  depends  upon
       the C locale and the ASCII character encoding, whereas the former is independent of locale and character set.  (Note that
       the brackets in these class names are part of the symbolic names, and must  be  included  in  addition  to  the  brackets
       delimiting  the  bracket  expression.)   Most  meta-characters lose their special meaning inside bracket expressions.  To
       include a literal ] place it first in the list.  Similarly, to include a literal ^ place it anywhere but first.  Finally,
       to include a literal - place it last.

   Anchoring
       The  caret  ^ and the dollar sign $ are meta-characters that respectively match the empty string at the beginning and end
       of a line.

   The Backslash Character and Special Expressions
       The symbols \< and \> respectively match the empty string at the beginning and end of a word.  The symbol \b matches  the
       empty  string at the edge of a word, and \B matches the empty string provided it's not at the edge of a word.  The symbol
       \w is a synonym for [[:alnum:]] and \W is a synonym for [^[:alnum:]].

   Repetition
       A regular expression may be followed by one of several repetition operators:
       ?      The preceding item is optional and matched at most once.
       *      The preceding item will be matched zero or more times.
       +      The preceding item will be matched one or more times.
       {n}    The preceding item is matched exactly n times.
       {n,}   The preceding item is matched n or more times.
       {,m}   The preceding item is matched at most m times.
       {n,m}  The preceding item is matched at least n times, but not more than m times.

   Concatenation(n. 串联，连结)
       Two regular expressions may be concatenated; the resulting regular expression matches any string formed by  concatenating
       two substrings that respectively match the concatenated expressions.

   Alternation
       Two  regular  expressions  may  be  joined  by  the infix operator |; the resulting regular expression matches any string
       matching either alternate expression.

   Precedence
       Repetition takes precedence over concatenation, which in turn takes precedence over alternation.  A whole expression  may
       be enclosed in parentheses to override these precedence rules and form a subexpression.

   Back References and Subexpressions
       The  back-reference  \n,  where  n  is  a single digit, matches the substring previously matched by the nth parenthesized
       subexpression of the regular expression.

   Basic vs Extended Regular Expressions
       In basic regular expressions the meta-characters ?, +, {, |, (, and  )  lose  their  special  meaning;  instead  use  the
       backslashed versions \?, \+, \{, \|, \(, and \).

       Traditional  egrep  did  not support the { meta-character, and some egrep implementations support \{ instead, so portable
       scripts should avoid { in grep -E patterns and should use [{] to match a literal {.

       GNU grep -E attempts to support traditional usage by assuming that { is not special if  it  would  be  the  start  of  an
       invalid  interval  specification.  For example, the command grep -E '{1' searches for the two-character string {1 instead
       of reporting a syntax error in the regular expression.  POSIX.2 allows  this  behavior  as  an  extension,  but  portable
       scripts should avoid it.

ENVIRONMENT VARIABLES
       The behavior of grep is affected by the following environment variables.

       The  locale  for  category LC_foo is specified by examining the three environment variables LC_ALL, LC_foo, LANG, in that
       order.  The first of these variables that is set  specifies  the  locale.   For  example,  if  LC_ALL  is  not  set,  but
       LC_MESSAGES  is set to pt_BR, then the Brazilian Portuguese locale is used for the LC_MESSAGES category.  The C locale is
       used if none of these environment variables are set, if the locale catalog is not installed, or if grep was not  compiled
       with national language support (NLS).

       GREP_OPTIONS
              This  variable  specifies  default  options  to  be  placed  in  front  of  any explicit options.  For example, if
              GREP_OPTIONS  is  '--binary-files=without-match  --directories=skip',  grep  behaves  as  if   the   two   options
              --binary-files=without-match  and  --directories=skip  had  been  specified  before  any explicit options.  Option
              specifications are separated by whitespace.  A backslash escapes the next character, so it can be used to  specify
              an option containing whitespace or a backslash.

       GREP_COLOR
              This  variable  specifies  the  color  used  to  highlight matched (non-empty) text.  It is deprecated in favor of
              GREP_COLORS, but still supported.  The mt, ms, and mc capabilities of GREP_COLORS have priority over it.   It  can
              only  specify  the  color used to highlight the matching non-empty text in any matching line (a selected line when
              the -v command-line option is omitted, or a context line when -v is specified).  The default is 01;31, which means
              a bold red foreground text on the terminal's default background.

       GREP_COLORS
              Specifies  the  colors  and other attributes used to highlight various parts of the output.  Its value is a colon-
              separated list of capabilities that defaults to ms=01;31:mc=01;31:sl=:cx=:fn=35:ln=32:bn=32:se=36 with the rv  and
              ne boolean capabilities omitted (i.e., false).  Supported capabilities are as follows.

              sl=    SGR substring for whole selected lines (i.e., matching lines when the -v command-line option is omitted, or
                     non-matching lines when -v is specified).  If however the boolean rv capability  and  the  -v  command-line
                     option  are  both specified, it applies to context matching lines instead.  The default is empty (i.e., the
                     terminal's default color pair).

              cx=    SGR substring for whole context lines (i.e., non-matching lines when the -v command-line option is omitted,
                     or  matching  lines  when  -v  is specified).  If however the boolean rv capability and the -v command-line
                     option are both specified, it applies to selected non-matching lines instead.  The default is empty  (i.e.,
                     the terminal's default color pair).

              rv     Boolean  value  that reverses (swaps) the meanings of the sl= and cx= capabilities when the -v command-line
                     option is specified.  The default is false (i.e., the capability is omitted).

              mt=01;31
                     SGR substring for matching non-empty text in any matching line (i.e., a selected line when the -v  command-
                     line  option  is  omitted,  or a context line when -v is specified).  Setting this is equivalent to setting
                     both ms= and mc= at once to the same value.  The default is a bold red text  foreground  over  the  current
                     line background.

              ms=01;31
                     SGR  substring for matching non-empty text in a selected line.  (This is only used when the -v command-line
                     option is omitted.)  The effect of the sl= (or cx= if rv) capability remains active  when  this  kicks  in.
                     The default is a bold red text foreground over the current line background.

              mc=01;31
                     SGR  substring  for matching non-empty text in a context line.  (This is only used when the -v command-line
                     option is specified.)  The effect of the cx= (or sl= if rv) capability remains active when this  kicks  in.
                     The default is a bold red text foreground over the current line background.

              fn=35  SGR substring for file names prefixing any content line.  The default is a magenta text foreground over the
                     terminal's default background.

              ln=32  SGR substring for line numbers prefixing any content line.  The default is a green text foreground over the
                     terminal's default background.

              bn=32  SGR substring for byte offsets prefixing any content line.  The default is a green text foreground over the
                     terminal's default background.

              se=36  SGR substring for separators that are inserted between selected  line  fields  (:),  between  context  line
                     fields, (-), and between groups of adjacent lines when nonzero context is specified (--).  The default is a
                     cyan text foreground over the terminal's default background.

              ne     Boolean value that prevents clearing to the end of line using Erase in Line (EL) to Right (\33[K) each time
                     a  colorized  item ends.  This is needed on terminals on which EL is not supported.  It is otherwise useful
                     on terminals for which the back_color_erase (bce) boolean terminfo capability  does  not  apply,  when  the
                     chosen  highlight  colors  do not affect the background, or when EL is too slow or causes too much flicker.
                     The default is false (i.e., the capability is omitted).

              Note that boolean capabilities have no =...  part.  They are omitted (i.e., false) by default and become true when
              specified.

              See  the  Select  Graphic  Rendition  (SGR)  section  in  the  documentation of the text terminal that is used for
              permitted values and their meaning as character attributes.   These  substring  values  are  integers  in  decimal
              representation  and can be concatenated with semicolons.  grep takes care of assembling the result into a complete
              SGR sequence (\33[...m).  Common values to concatenate include 1 for bold, 4 for underline, 5  for  blink,  7  for
              inverse,  39  for  default foreground color, 30 to 37 for foreground colors, 90 to 97 for 16-color mode foreground
              colors, 38;5;0 to 38;5;255 for 88-color and 256-color modes foreground colors, 49 for default background color, 40
              to  47  for background colors, 100 to 107 for 16-color mode background colors, and 48;5;0 to 48;5;255 for 88-color
              and 256-color modes background colors.

       LC_ALL, LC_COLLATE, LANG
              These variables specify the locale for the LC_COLLATE category, which determines the collating  sequence  used  to
              interpret range expressions like [a-z].

       LC_ALL, LC_CTYPE, LANG
              These variables specify the locale for the LC_CTYPE category, which determines the type of characters, e.g., which
              characters are whitespace.

       LC_ALL, LC_MESSAGES, LANG
              These variables specify the locale for the LC_MESSAGES category, which determines the language that grep uses  for
              messages.  The default C locale uses American English messages.

       POSIXLY_CORRECT
              If  set, grep behaves as POSIX.2 requires; otherwise, grep behaves more like other GNU programs.  POSIX.2 requires
              that options that follow file names must be treated as file names; by default, such options are  permuted  to  the
              front  of  the  operand  list  and  are  treated  as options.  Also, POSIX.2 requires that unrecognized options be
              diagnosed as "illegal", but since they are not really  against  the  law  the  default  is  to  diagnose  them  as
              "invalid".  POSIXLY_CORRECT also disables _N_GNU_nonoption_argv_flags_, described below.

       _N_GNU_nonoption_argv_flags_
              (Here  N  is  grep's  numeric process ID.)  If the ith character of this environment variable's value is 1, do not
              consider the ith operand of grep to be an option, even if it appears to be one.  A shell can put this variable  in
              the  environment  for  each  command  it  runs,  specifying  which  operands are the results of file name wildcard
              expansion and therefore should not be treated as options.  This behavior is available only with the GNU C library,
              and only when POSIXLY_CORRECT is not set.

EXIT STATUS
       Normally,  the  exit  status  is  0  if  selected  lines are found and 1 otherwise.  But the exit status is 2 if an error
       occurred, unless the -q or --quiet or --silent option is used and a selected line is found.  Note,  however,  that  POSIX
       only  mandates,  for programs such as grep, cmp, and diff, that the exit status in case of error be greater than 1; it is
       therefore advisable, for the sake of portability, to use logic that tests for this general condition  instead  of  strict
       equality with 2.
-----------------------------------------------------------------------------------------------------------------------------------------
