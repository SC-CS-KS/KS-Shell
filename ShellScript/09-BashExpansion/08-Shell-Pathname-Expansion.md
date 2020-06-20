# 08.Shell Pathname Expansion

Pathname Expansion
       After  word  splitting, unless the -f option has been set, bash scans each word for the characters *, ?, and [.  If one of these characters
       appears, then the word is regarded as a pattern, and replaced with an alphabetically sorted list of file names matching the pattern.  If no
       matching  file  names are found, and the shell option nullglob is disabled, the word is left unchanged.  If the nullglob option is set, and
       no matches are found, the word is removed.  If the failglob shell option is set, and no matches are found, an error message is printed  and
       the  command  is  not executed.  If the shell option nocaseglob is enabled, the match is performed without regard to the case of alphabetic
       characters.  When a pattern is used for pathname expansion, the character ''.''  at the start of a name or immediately  following  a  slash
       must  be  matched explicitly, unless the shell option dotglob is set.  When matching a pathname, the slash character must always be matched
       explicitly.  In other cases, the ''.''  character is not treated specially.  See the description of shopt below under  SHELL  BUILTIN  COM-
       MANDS for a description of the nocaseglob, nullglob, failglob, and dotglob shell options.

       The  GLOBIGNORE  shell variable may be used to restrict the set of file names matching a pattern.  If GLOBIGNORE is set, each matching file
       name that also matches one of the patterns in GLOBIGNORE is removed from the list of matches.  The file names ''.''  and ''..''  are always
       ignored  when GLOBIGNORE is set and not null.  However, setting GLOBIGNORE to a non-null value has the effect of enabling the dotglob shell
       option, so all other file names beginning with a ''.''  will match.  To get the old behavior of ignoring file names beginning with a ''.'',
       make ''.*''  one of the patterns in GLOBIGNORE.  The dotglob option is disabled when GLOBIGNORE is unset.

       Pattern Matching

       Any  character that appears in a pattern, other than the special pattern characters described below, matches itself.  The NUL character may
       not occur in a pattern.  A backslash escapes the following character; the escaping backslash is discarded when matching.  The special  pat-
       tern characters must be quoted if they are to be matched literally.

       The special pattern characters have the following meanings:

       *      Matches any string, including the null string.
       ?      Matches any single character.
       [...]  Matches  any  one  of the enclosed characters.  A pair of characters separated by a hyphen denotes a range expression; any character
              that sorts between those two characters, inclusive, using the current locale's collating sequence and character set, is matched.  If
              the  first  character following the [ is a !  or a ^ then any character not enclosed is matched.  The sorting order of characters in
              range expressions is determined by the current locale and the value of the LC_COLLATE shell variable, if set.  A - may be matched by
              including it as the first or last character in the set.  A ] may be matched by including it as the first character in the set.

              Within  [  and ], character classes can be specified using the syntax [:class:], where class is one of the following classes defined
              in the POSIX.2 standard:
              alnum alpha ascii blank cntrl digit graph lower print punct space upper word xdigit
              A character class matches any character belonging to that class.  The word character class matches letters, digits, and the  charac-
              ter _.

              Within  [  and ], an equivalence class can be specified using the syntax [=c=], which matches all characters with the same collation
              weight (as defined by the current locale) as the character c.

              Within [ and ], the syntax [.symbol.] matches the collating symbol symbol.

       If the extglob shell option is enabled using the shopt builtin, several extended pattern matching operators are recognized.  In the follow-
       ing  description, a pattern-list is a list of one or more patterns separated by a |.  Composite patterns may be formed using one or more of
       the following sub-patterns:

              ?(pattern-list)
                     Matches zero or one occurrence of the given patterns
              *(pattern-list)
                     Matches zero or more occurrences of the given patterns
              +(pattern-list)
                     Matches one or more occurrences of the given patterns
              @(pattern-list)
                     Matches exactly one of the given patterns
              !(pattern-list)
                     Matches anything except one of the given patterns

Quote Removal
After the preceding expansions, all unquoted occurrences of the characters \, ', and " that did not result from one of the above expansions 
are removed.
