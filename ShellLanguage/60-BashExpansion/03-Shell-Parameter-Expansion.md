# 03.Shell Parameter Expansion

   Parameter Expansion
       The  '$'  character  introduces  parameter  expansion,  command  substitution, or arithmetic expansion.  The parameter name or symbol to be expanded may be enclosed in braces, which are optional but serve to protect the variable to be expanded from characters immediately following it which could be interpreted as part of the name.

       When  braces  are  used, the matching ending brace is the first '}' not escaped by a backslash or within a quoted string, and not within an
       embedded arithmetic expansion, command substitution, or paramter expansion.

       ${parameter}
              The value of parameter is substituted.  The braces are required when parameter is a positional parameter with more than  one  digit,
              or when parameter is followed by a character which is not to be interpreted as part of its name.

       If  the  first  character  of parameter is an exclamation point, a level of variable indirection is introduced.  Bash uses the value of the
       variable formed from the rest of parameter as the name of the variable; this variable is then expanded and that value is used in  the  rest
       of  the  substitution,  rather  than  the  value of parameter itself.  This is known as indirect expansion.  The exceptions to this are the
       expansions of ${!prefix*} and ${!name[@]} described below.  The exclamation point must immediately follow the left brace in order to intro-
       duce indirection.

       In  each of the cases below, word is subject to tilde expansion, parameter expansion, command substitution, and arithmetic expansion.  When
       not performing substring expansion, bash tests for a parameter that is unset or null; omitting the colon results  in  a  test  only  for  a
       parameter that is unset.

       ${parameter:-word}
              Use Default Values.  If parameter is unset or null, the expansion of word is substituted.  Otherwise, the value of parameter is sub-
              stituted.
       ${parameter:=word}
              Assign Default Values.  If parameter is unset or null, the expansion of word is assigned to parameter.  The value  of  parameter  is
              then substituted.  Positional parameters and special parameters may not be assigned to in this way.
       ${parameter:?word}
              Display  Error  if  Null or Unset.  If parameter is null or unset, the expansion of word (or a message to that effect if word is not
              present) is written to the standard error and the shell, if it is not interactive, exits.  Otherwise, the value of parameter is sub-
              stituted.
       ${parameter:+word}
              Use Alternate Value.  If parameter is null or unset, nothing is substituted, otherwise the expansion of word is substituted.
       ${parameter:offset}
       ${parameter:offset:length}
              Substring  Expansion.   Expands to up to length characters of parameter starting at the character specified by offset.  If length is
              omitted, expands to the substring of parameter starting at the character specified by offset.   length  and  offset  are  arithmetic
              expressions (see ARITHMETIC EVALUATION below).  length must evaluate to a number greater than or equal to zero.  If offset evaluates
              to a number less than zero, the value is used as an offset from the end of the value of parameter.  If parameter is @, the result is
              length positional parameters beginning at offset.  If parameter is an array name indexed by @ or *, the result is the length members
              of the array beginning with ${parameter[offset]}.  Substring indexing is zero-based unless the positional parameters  are  used,  in
              which case the indexing starts at 1.

       ${!prefix*}
       ${!prefix@}
              Expands to the names of variables whose names begin with prefix, separated by the first character of the IFS special variable.

       ${!name[@]}
       ${!name[*]}
              If  name  is an array variable, expands to the list of array indices (keys) assigned in name.  If name is not an array, expands to 0
              if name is set and null otherwise.  When @ is used and the expansion appears within double quotes, each key expands  to  a  separate
              word.

       ${#parameter}
              The  length  in characters of the value of parameter is substituted.  If parameter is * or @, the value substituted is the number of
              positional parameters.  If parameter is an array name subscripted by * or @, the value substituted is the number of elements in  the
              array.

       ${parameter#word}
       ${parameter##word}
              The  word  is  expanded  to  produce  a pattern just as in pathname expansion.  If the pattern matches the beginning of the value of
              parameter, then the result of the expansion is the expanded value of parameter with the shortest matching pattern (the  ''#''  case)
              or the longest matching pattern (the ''##'' case) deleted.  If parameter is @ or *, the pattern removal operation is applied to each
              positional parameter in turn, and the expansion is the resultant list.  If parameter is an array variable subscripted with @  or  *,
              the pattern removal operation is applied to each member of the array in turn, and the expansion is the resultant list.

       ${parameter%word}
       ${parameter%%word}
              The  word is expanded to produce a pattern just as in pathname expansion.  If the pattern matches a trailing portion of the expanded
              value of parameter, then the result of the expansion is the expanded value of parameter with  the  shortest  matching  pattern  (the
              ''%''  case)  or  the  longest matching pattern (the ''%%'' case) deleted.  If parameter is @ or *, the pattern removal operation is
              applied to each positional parameter in turn, and the expansion is the resultant list.  If  parameter  is  an  array  variable  sub-
              scripted  with @ or *, the pattern removal operation is applied to each member of the array in turn, and the expansion is the resul-
              tant list.

       ${parameter/pattern/string}
       ${parameter//pattern/string}
              The pattern is expanded to produce a pattern just as in pathname expansion.  Parameter is expanded and the longest match of  pattern
              against its value is replaced with string.  In the first form, only the first match is replaced.  The second form causes all matches
              of pattern to be replaced with string.  If pattern begins with #, it must match at the beginning of the expanded value of parameter.
              If  pattern  begins  with %, it must match at the end of the expanded value of parameter.  If string is null, matches of pattern are
              deleted and the / following pattern may be omitted.  If parameter is @ or *, the substitution operation is  applied  to  each  posi-
              tional  parameter  in turn, and the expansion is the resultant list.  If parameter is an array variable subscripted with @ or *, the
              substitution operation is applied to each member of the array in turn, and the expansion is the resultant list.
