# Java-Compiler
simple java compiler

This file contains details of modification done to support a selected group of functions in java.  

Howard Szeto
CS451 HW1

For the implementation of division and modulo operators, instructions from Ch1
were followed to modify the codes. Only few problems were encountered during the
compile step. One of the issues was the failure of the compiler to recognize the
statement Integer.parseInt(), which was resolved by importing the appropriate
classes. The second issue was the failure of the compiler to recognize classes
in the pass package. It turned out that the cause was the calling of non-static
functions from static functions. For the unary plus, JUnaryExpression was
modified by adding JPositiveOp, which was similar to JNegateOp. However, the
body codegen() for JPositiveOp was empty. For this assignment, int bitwise
operators were not implemented due to time problem.

Division test cases were described in the Ch1 of the book. For the modulo tests,
the following pass cases were tested: 0%42, 42%4, and 7%3. For the fail case,
the first argument was of type char instead of int to ensure type checking is
working. For both division and modulo tests, the expected results were generated
from the compiler. However, division by zero was not tested in this
case. UnaryPlus pass test case was +6 and the fail case was +'c'. Again, both
cases generated expected results. The pass test +6 was chosen due to the
simpleness of the expression.


Howard Szeto
CS451 HW2

Building the scanner code associated with multi-line comments was relatively
simply. Program would continue to scan for the next character in an infinite
while loop until a '*' character was scanned, which would set a boolean variable
onWatch to be true. If the next char was a '/' character, the program will read
the next char and exit the loop. If the infinite scanning reaches the end of the
stream, an error message will be reported. Otherwise, the variable onWatch is
set to be false to avoid accepting false cases.

For the operators and keywords, the missing tokens were added to the
TokenInfo.java and Scanner.java.
Shown below were the missing operators added:
MINUS_ASSIGN("-="), GE(">="), LT("<"), NOT_EQUAL("!="), BAND("&"),
AND_ASSIGN("&="), BOR("|"), LOR("||"), OR_ASSIGN("|="), BITFLIP("~"),
STAR_ASSIGN("*="), DIV_ASSIGN("/="), MOD_ASSIGN("%="), XOR("^"),
XOR_ASSIGN("^="), LSHIFT("<<"), LSHIFT_ASSIGN("<<="), UNSIGNED_RSHIFT(">>>"),
UNSIGNED_RSHIFT_ASSIGN(">>>="), SIGNED_RSHIFT(">>"),
SIGNED_RSHIFT_ASSIGN(">>="), QUESTION("?"), COLON(":"), ARROW("->")

Shown below were the missing keywords as well as the java literals added:
ASSERT("assert"), BREAK("break"), BYTE("byte"), CASE("case"), CATCH("catch"),
CONST("const"), CONTINUE("continue"), DEFAULT("default"), DO("do"),
DOUBLE("double"), ENUM("enum"), FINAL("final"), FINALLY("finally"),
FLOAT("float"), FOR("for"), GOTO("goto"), IMPLEMENTS("implements"),
INTERFACE("interface"), LONG("long"), NATIVE("native"), SHORT("short"),
STRICTFP("strictfp"), SWITCH("switch"), SYNCHRONIZED("synchronized"),
THROW("throw"), THROWS("throws"), TRANSIENT("transient"), TRY("try"),
VOLATILE("volatile"), DOUBLE_LITERAL("<DOUBLE_LITERAL>"),
FLOAT_LITERAL("<FLOAT_LITERAL>"), LONG_LITERAL("<LONG_LITERAL>")

For the three different forms number representation (octal, decimal, and
hexadecimal), their exact images were passed in with the corresponding literals
to the TokenKind constructor. Error would be reported if the number input string
ended with the exponent indicator (p or e) or an exponent indicator with a sign
behind. A scanner error would also be generated if no hexadecimal digit was
detected after scanning 0x. This scanner would also accept underscore (_) in the
number input string, a feature of Java SE 8 Edition. However, it sometimes
failed to report scanner error when illegal usage of underscore was
entered. Furthermore, incorrect octal input would not be rejected at the scanner
stage and impossible number representation (due to the memory limit of the
literal type) would be accepted by the scanner. Their implementations would be
left for the parser or analyzer.

Selected few unicodes for char literal were also implemented into the
scanner. Only the unicode escapes were used as they were required.

Due to the fact that underscore features were implemented at the last minute,
some illegal usages of underscore were not rejected. Furthermore, part of the
codes for reading characters was very similar. Hence, the scanner would be
improved upon on in the near future.

For test cases, different forms of legal inputs for each literal were
tested. Underscores, hexadecimals, exponent indicators, floating point suffixes,
multi-line comments, and unicode \u0008 were tested. All the selected tests for
pass scenario were accepted and this range of tests should cover all the
possible scenarios of input cases.  However, some forms of illegal underscore
cases were failed to be rejected by scanner.


Howard Szeto
CS451 HW3

All new features in this assignment modified Parser.java in some forms

***Long parsing (long)
--Additional modification: Type.java
--Created JLiteralLong.java

The code for handling the basic type long was added whether parsing of type int
was taking place. The modifications were done in both Parser.java and Type.java
files. A java class, JLiteralLong, was also created using JLiteralInt as a
template.

###########################################################################
***Conditional Expressions (?:)
--Created JConditionalExpression.java

New method conditionalExpression() was built to handle the ternary
operation. This method was called within assignmentExpression() to ensure the
operator precedence was maintained (since ternary is one level higher than
assignment operator). Furthermore, the method conditionalExpression() was code
to read in all three parts of the ternary operator. A new class named
JConditionalExpression.java was built to handle the ternary operator as a
separate JExpression. It had a similar setup compared to JIfStatement.java. 

###########################################################################
***Logical Or Expression (||)
--Additional modification: JBooleanBinaryExpression.java

New method conditionalOrExpression() was added to the Parser.java file using
conditionalAndExpression() as the model. To ensure correct operator precedence
was implemented, this method was called from method conditionalExpression(). A
new class JLogicalOrOp was built in the file JBooleanBinaryExpression.java. The
whole class was manufactured using JLogicalAndOp as a guide.

###########################################################################
***Prefix-- and Postfix++ (--expr ,expr++)
--Additional modification: JUnaryExpression.java

Two new classes were added in for JUnaryExpression.java: JPreDecrementOp and
JPostIncrementOp. They were built using JPreIncrementOp and JPostDecrementOp as
the guides. Furthermore, the methods statementExpression(), unaryExpression()
and postfixExpression() in Parser.java were modified to handle prefix-- and
postfix++ scenarios.

###########################################################################
***Interface (interface, implements)
--Additional modification: JClassDeclaration.java
--Created JInterfaceDeclaration.java

In Parser.java, method classDeclaration was modified to handle keyword
"implements" as well as "interface". If the keyword implements was detected, it
would continue to add qualified identifier into an ArrayList until there wasn't
anymore commas to consume. In the case that keyword interface was detected in
the place of class, boolean isInterface would be set to be true. In this
scenario, having modifiers (except abstract) and the keyword extends would
trigger a parsing error. This method would return a JAST (JInterfaceDeclaration
or JClassDeclaration) depending on the scenario. 

In the file JClassDeclaration.java, the constructor and list of variables were
modified to handle the ArrayList<Type> interfaces, which contained a list of
interfaces implemented for the class.

New file JInterfaceDeclaration.java was constructed using JClassDeclaration.java
as the template. As of HW3, the program failed to enforce the limit of the
interface's declared variables and functions (methods could be implemented and
values of variables could be changed). However, the method modifiers() as well
as localVariableDeclarationStatement() were modified to handle different
modifiers for variables as well as the keyword "final".

###########################################################################
***Throw (throw)
--Created JThrowStatement.java

In the Parser.java file, the method statement() was modified to handle the
keyword "throw". The coding of this portion was similar to that of return. Java
class JThrowStatement was also built using java class JReturnStatement.java as
the template.

###########################################################################
***Throws (throws)
--Additional modification: JMethodDeclaration.java

In the procedure memberDecl, code was added after the parsing of parameters to
handle the keyword "throws". The program would continue to add exceptions
(treated as identifier) until there wasn't available comma to
consume. Furthermore, constructor and list of variables of JMethodDeclaration
was modified to deal with the arrayList<String> of exceptions.

###########################################################################
***Try/Catch/Finally (try,catch,finally)
--Created JTryStatement.java, JCatchStatement

The method statement() in Parser.java was modified to handle the keyword
"try". The other two keywords, "catch" and "finally", could only be parsed
within the keyword "try" to reflect the fact that "catch" and "finally" could
not be used independed of "try". After parsing the statements associated with
"try", the program would demand at least one "catch" and continue to parsing
cases of "catch". Each scenario of catch would cause a creation of new
JCatchStatement. Afterward, the program would attempt to parse "finally" if
there existed such keyword. 

JCatchStatement.java was built to hold two variables: condition and
body. JTryStatement.java contained four fields: body, arrayList of the catches,
finallyLine (an int to hold the line number where finally was detected), and
willBeExecuted (the body from "finally"). No new class was declared for
"finally" to reduce the coding needed.

###########################################################################
***For (for)
--Created JForStatement.java

In Parser.java, the method statement was edited to handle keyword "for". Two
helper methods, forParExpression() and lookForSEMIorCOLON() were constructed to
reduce the amount of coding in statement(). In the method forParExpression(), an
array of two or three elements would be returned. If the returned array had two
elements, it suggested that a colon was detected in the scan ahead method
lookForSEMIorCOLON (for-each loop). Otherwise, it was a conventional for loop to
be parsed.

For the JForStatement.java file, an array named setups and variable body were
used to hold the necessary information for the for-loop. Using an array to hold
all the information within the parenthesis simplified the codes and allowed one
to avoid overloading the constructor. However, this left much of the coding to
the analyze() and codegen().

###########################################################################
***Do-while loop (do{...}while())
--Created JDoWhileStatement.java

Parsing of the do-while loop was very similar to that of the while loop. This
was also the case of the JDoWhileStatement.java file. 

###########################################################################
***Switch/case statement (switch,case,default,break)
--Created JCaseStatement.java, JDefaultStatement.java, JBreakStatement.java,
  JSwitchStatement.java

Parsing of keyword "break" was similar to that of keyword "return" when no
expression was returned.

For the parsing of switch, a different approach was used compared to that of
try/catch/finally. Keywords "default" and "case" were not parsed within the
parsing of "switch". As a result, orphan cases and defaults could not be
detected effectively. However, parsing errors would be detected if LCURLY was
not detected after the keywords "switch", "case", or "default". Furthermore,
duplicate default label and missing the keyword "case" or "default" after the
LCURLY would result in parser error. However, our parser would accept "case"
after the "default" was declared already. 

JSwitchStatement.java was composed of two fields: variable and arrayList of
cases. JCaseStatement.java contained the fields condition and
body. JDefaultStatement.java only contained the variable body.

###########################################################################
Reflection:

This programming assignment demanded more time than expected. As a result, it
was not completed within the deadline assigned. Furthermore, some of the
comments were not edited to reflect the actual codes.

Some of the possible improvements would be catching more parsing errors, such as
the usage of case/default outside of the switch statement, failure to reject
illegal modifiers for methods and variables in the interface. Since timing was a
huge issue in this assignment, the path of least resistance was taken for the
coding. This resulted in potential inefficient execution of parts of the code as
well as reduced readability.


Howard Szeto
HW4

Implementation for int,double,float

Search for int in the j--.jj file and place the double and float equivalents
into the file. New JLiterlDouble and JLiteralFloat files are made in order to
make the double or float image. The grammar for int, double, and float are
implemented using the grammar from JSE8. It is to be noticed that the octal and
hex representation for double and float are not implemented.

Interfaces and Implements

Edited the function of JClassDeclaration. Return type is changed to JAST and
added in the function for detecting interface. The keyword extends can't be used
by interface. The return type is determined by whether isInterface is true or
not.

for loop (classics and for-each)

Add in the keyword for in the function statement(). Use LOOKAHEAD to determine
whether a classic for loop or for-each loop is being parsed.

Extra

A place holder is added in to ensure that method declaration's arguments matched
with the constructor.

Testing

Interfaces and implements as well as int are working as intended. However, for loop, double/float are not working as intended. Double was not read in correctly and an error was induced. For future improvement, these errors will be edited to resolve the issue. 


Howard Szeto
CS451 HW5
-----------------------------------------------------------------------------
***Implementation of longs (long)

The following java files were modified to allow for the code generation of
primitive type long:
Type.java
JComparison.java
JCastOp.java
JLiteralLong.java
JAssignment.java
JBinaryExpression.java
JBinaryBooleanExpression.java
JArrayInitializer.java
JArrayExpression.java
JMethodDeclaration.java
JReturnStatement.java
JUnaryExpression.java
JVariable.java

For the following files, analyze() was modified to handle the Type.LONG
scenarios and codegen was modified to use the appropriate instruction codes to
deal with type long. In the modification for JUnaryExpression, --/++expr and
expr--/++,as well as NegateOp and PositiveOp, were modified to enable the longs
to use the same utilities as type int. For comparison (<, >=, and ==), the
analyze() was modified to enable the comparison of type long. Furthermore, LCMP
followed by IF_ICMPEQ/IF_ICMPNE was used in the codegen to generate the desired
JVM codes. For casting, boxing and un-boxing were added for type
long. Furthermore, casting from/to long for int and char were included using
L2I, I2L, etc.

Last but not least, the file JLiteralLong was modified with the appropriate
analyze() and codegen() to generate the desired effect.
-----------------------------------------------------------------------------
***Implementation of logical OR (||)

Only the file of JBooleanBinaryExpression.java was modified. The analyze()
portion was copied from analyze() of logical AND (&&). For the codegen, a
short-circuit mechanism, similar to that of logical AND, was
implemented. 
-----------------------------------------------------------------------------
***Implementation of conditional expression

Only the file of JConditionalExpression.java was modified. The analyze() first
checked to ensure the condition is of type boolean. Afterward, it confirmed both
the then part and alternative part to be of the same type. The type of the
conditional expression was then set to be the same as that of the then part. In
the codegen, the coding was similar to JIfStatement to generate the then/else
part of the code.
-----------------------------------------------------------------------------
***Implementation of break

Analysis for break was missing due to the lack of understanding of
Context. However, the codegen of JBreakStatement.java as well as all subclasses
of JStatement were modified significantly to enable the ability to fulfill the
jump to the end. This was done by defining an abstract codegen(CLEmitter,String)
in the JStatement.java, ensuring that all JStatement can pass the String out
defined in the higher scope to the lower scope. However, the
codegen(CLEmitter,String) of JExpression subclasses merely called
codegen(CLEmitter) (Since they could not have a JStatement).

-----------------------------------------------------------------------------
***Implementation of classic for

Analysis for classic for loop is done by converting the for loop into a JBlock
composing of the first part of the for loop and a JWhileStatement. The condition
of the JWhileStatement was the second part of the for loop. The main body of the
JWhileStatement was a JBlock composed of the body of the for loop as well as the
last part of the for loop. Since the return type of the analysis was JBlock, no
codegen was needed for JForStatement.

-----------------------------------------------------------------------------
***Implementation of switch/case/default

Analysis of JSwitchStatement: checked the JExpression to ensure it is of type
int. Currently, type char and type String is not supported. Then each statement
in the arrayList was analyzed. Same procedure for the analyze() of
JCaseStatement was used. For JDefaultStatement, only items in ArrayList were
analyzed.  For the codegen of JSwitchStatement, one label was generated for each
case/Default statement. Then a for loop would be used to generate the comparison
part of the codegen (getMyCondition was a method used to retrieve the
JExpression in the JCaseStatement). Afterward, the labels as well as the codegen
for the cases were added. Lastly, the label of out was added. 

Testing cases:
declaring a long variable
casting from int to long
casting from char to long
Unary operation with long
Conditional Expression
for loop
break statement at multiple places
switch/case/default

Result:

Declaring a long variable and casting seem to provide intended results. However,
++/-- pre/post expressions did not have intended result. iinc was still being
used in the codegen. The conditional expression and the for loop seemed to be
working. The break statement appeared to work with loops and
switch/case/default. However, the switch/case/default generated a different
codegen compared to the one generated by java. That was because tableswitch
instruction was not used. Instead, a strategy of continuous comparison with a
jump statement(when true) to the desired label was used.
