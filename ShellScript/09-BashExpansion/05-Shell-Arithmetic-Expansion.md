# 05.Shell Arithmetic Expansion

Arithmetic Expansion
Arithmetic expansion allows the evaluation of an arithmetic expression and the substitution of  the  result.   The  format  for  arithmetic
       expansion is:

              $((expression))

       The  expression  is  treated  as  if it were within double quotes, but a double quote inside the parentheses is not treated specially.  All
       tokens in the expression undergo parameter expansion, string expansion, command substitution, and quote removal.  Arithmetic expansions may
       be nested.

       The  evaluation is performed according to the rules listed below under ARITHMETIC EVALUATION.  If expression is invalid, bash prints a mes-
       sage indicating failure and no substitution occurs.

   
