# 1 Installation and Configuration of Compiler and Interpreter
### 1.1 Quick Start
[RCML Quick Start for Windows](https://youtu.be/gSu8K0n8g3Y)

[RCML Quick Start for Linux](https://youtu.be/AdjV8Ruke_Y)

Go to *SourceForge* project page at:

http://sourceforge.net/projects/rcmlang/files/

From the folder of your OS, download the archive with files of *rcml_build_X.zip* compiler and the archive with files of *rcml_modules_build_X.zip* modules, where *X* is compiler version. Versions for these two archives must be the same.

Extract both of the downloaded archives to the same folder.

Go to the folder with the files extracted and create *config.ini* file there with the following contents:
```
[robot_modules]
module = test
[function_modules]
[control_modules]
[repository]
[lib_search_paths]
```
In the current folder, create another file called *hello.rcml* with the following contents:
```
function main() {
	robot_test->print("Hello world!\n", 0);
}
```
This will be your first program in *RCML*, consisting of a single operator – a function calling output of the line *“Hello world!\n”* of the test virtual robot *robot_test* with a delay of 0 sec.

To compile a program, run the command line editor in the current folder and execute the following command:
```
rcml_compiler.exe hello.rcml hello.rcml.pc
```
A file of compiled program hello.pc will appear in the current folder, as shown in the figure 1.

![](http://rcml.info/images/aboutrcml/573e37e1d43a24e1d732f5124dcddaa3.png)

Figure 1 Result of program compilation

To run the compiled program, execute the following command:
```
rcml_intepreter.exe hello.rcml
```
Please note that in this case you do not specify pc extension of the compiled program file. The example of successful execution of the file is shown in the figure 2.

![](http://rcml.info/images/aboutrcml/d1fbd4ab0bf7541d063ba5b5bcb575f5.png)

Figure 2 Result of successful program execution

### 1.2 Description of RCML Program Generation Process

You can write a program in *RCML* in any text editor, saving it in a plain text file with *rcml* extension.

*Rcml* extension is advisory in nature, in fact, a file can have any name supported by the file system of your OS.

Subsequently, this file is compiled by *RCML* language compiler, while executing a number of procedures to prepare the program for execution, namely:

* Validation of the program syntax;
* Validation of function calls, robots and transfer of parameters;
* Conversion of the program text into the byte code;
* Execution of various modifications to the byte code to speed up its execution and memory utilization optimization.

Upon successful program compilation, *RCML* language compiler generates a file with program byte code, with the recommended double extension *rcml.pc (PC – pseudo code)*. A file with the byte code includes binary content with a structure close to machine codes of the processor, executed by its own virtual machine due to certain features of *RCML* language.

The immediate program execution is performed by *RCML* language interpreter, as the name implies, interpreting byte code instructions through an internal virtual machine into real processor commands.

### 1.3 Installation and Configuration of RCML Compiler and Interpreter

The compiler and interpreter are available for downloading from the *SourceForge* project page:

http://sourceforge.net/projects/rcmlang/files/

The compiler and interpreter are two executable files that are to be put into the same folder. Additionally, in the folder you will have to create a text file *config.ini*, which is the *RCML* environment configuration file. The configuration is the same for the compiler and the interpreter.

This file may contain the following sections:

* *[robot_modules]* – the section for connecting robots' modules;
* *[function_modules]* - the section for connecting functional modules;
* *[control_modules]* - the section for connecting control modules; 
* *[choice_modules]* – the section for connecting the robot's choosing modules.

The name of the connected module is specified in the *module* property in the section of the same type as the module. If there are several connected modules of the same type, a separate line is allocated for each of them. Modules of a particular type are loaded in the order, in which they were listed in their section.

An example of setting connected modules in *config.ini*:
```
[robot_modules]
module = test
module = tarakan
module = uarm
module = lego_ev3
[function_modules]
module = math
[control_modules]
module = test
module = keyboard
module = gamepad
module = myo
[choice_modules]
module = avg
```
For modules of all types, folders named after the section are to be created in the folder with the compiler and interpreter *(robot_modules, function_modules, control_modules or choice_modules)*. For each module, in the folder corresponding to its type, a directory with the same name as the one of the module should be created; this directory will be further referred to as the module directory. The module itself is represented by a dynamic-link library file which should have the same name as the module, but with suffix _*module added*. The module file should be in the module folder. The module folder may contain other files required for module operation, e.g., configuration files.

Additionally, the *config.ini* file may contain following sections:

* *[statistics]* – this section may have only one property - *db_path* - the path to the database file where the robots will write statistics about the performed functions. If the database file does not exist, it will be created. If the database already exists, it will be amended. If this property is null or empty, no stats will be recorded.
* *[lib_search_paths]* – search paths for the compiled *RCML* libraries. In this section, the path property may specify the default path to the *RCML* library. There may be several paths, in case each path is set via a separate path property. The required library is searched by the specified paths in the same order in which the path in this section have been specified.
* *[locale_settings]* is the section of the current locale settings; it may contain only one property – locale, where the parameters of the current locale for *RCML* are specified as a string according to the rules adopted in the Linux environment. By default, the current locale is English; it is set by value *en_US.UTF-8*.

For example, to switch the current locale to Russian, set it to *ru_RU.UTF-8*.

*Important*! The *locale_settings* option is supported only in Windows OS.

The files of all supported locales may be downloaded from the respective directory at the official download page of the *SourceForge* project:

https://sourceforge.net/projects/rcmlang/files/translations/

You can read how to add your own locale to your *RCML* project in the locales repository webpage for *RCML*:
https://github.com/RobotControlTechnologies/RCML_translations

### 1.4 Details of Robot Modules

Robot modules hold one of the key positions in *RCML* language, because communication and transfer of commands to the physical robot is performed via them, see. the figure 3.

![](http://rcml.info/images/aboutrcml/cf2134f8d079887ec56f529e2254eb67.jpg)

Figure 3 The role of robot modules in communication with the physical robots

Robot module is responsible for sending commands from *RCML* language interpreter to one or more robots of a single class (or type) incorporated by this module. It is recommended to use a separate module for each robot class or type. *RCML* interpreter communicates with the robot module through the declared *API*, and that in turn communicates with each robot assigned to it. Thus, communication and control of a robot by the interpreter is implemented through the robot module, allowing to connect a wide range of robots to it. Read more on how to write your own robot module and connect it to the interpreter in Section ["Creating Own Modules for RCML"](http://rcml.info/eng/aboutrcml#paragraph-62).

Depending on their functionality, robot modules can additionally provide manual robot control by the control device represented by an appropriate module in *RCML* environment.

### 1.5 Details of Function Modules

You can add new features to *RCML* language through function modules that are not appropriate or can not be implemented in this language, for example, any complex calculations. Thus, function modules may allow RCML communication with other software through a separate *API*.

### 1.6 Details of Control Modules

Modules of this type are used for making decisions about choosing the next robot for performing a particular function. Alternatively, the choice may be based on previously accumulated statistical data about this function, robot, or the program. Using the choosing modules, it is possible to choose the robot by various different algorithms used in the modules. Usually, a separate module is a separate algorithm for choosing the robot.

Without these modules, the robots are chosen at the discretion of robots modules. Read more about the mechanism of choosing the robot in section ["Using the robot choosing module"](http://rcml.info/eng/aboutrcml#paragraph-199).

### 1.7 Compiling and Running RCML Program

As noted earlier, a program written in the *RCML* language should first be compiled into the byte-code, after which it can be invoked by the interpreter.

For compiling the program, start the *RCML* compiler; by default, the name of its executable (without extension, since it may vary depending on the OS) *is rcml_compiler*. The compiler is to be started with 2 parameters passed to it:
```
rcml_compiler <rcml_text_file> <rcml_pc_file>
```
The first parameter, *rcml_text_file*, is the path to the file with the *RCML* program, and the second parameter, *rcml_pc_file*, is the path to the file into which the byte-code of the program is to be written.

If compilation is successful, the file with the byte-code will be created, or overwritten if it already exists. And now it can be invoked using the *RCML* interpreter, by default, the name of the executable file is *rcml_interpreter*. At startup, the compiler expects a single parameter – the file with the byte-code. The syntax of the command for starting the interpreter:
```
rcml_interpreter <rcml_pc_file>
```
**Important**! The interpreter automatically appends the .pc extension to the *rcml_pc_file* path.

### 1.8 RCML command-line options

The command line of the *RCML* compiler has the following pattern:
```
rcml_compiler [--version] [--logfile log_file_path] <rcml_text_file> <rcml_pc_file>
```
* -- *version* - shows the current version of the *RCML* compiler, the list of versions supported by the *API* modules, and the build date. If this flag is specified, the compiler shows the information and immediately exits;
* --*logfile log_file_path* - writes the output of the RCML compiler to a file in the *log_file_path* part;
* *rcml_text_file* is the path to the file with textual representation of the *RCML* program; 
* *rcml_pc_file* is the path to the file that the compiled program is written to.

The command line of the *RCML* interpreter has the following pattern:
```
rcml_interpreter [--version] [--logfile log_file_path] <rcml_pc_file> [rcml_params]
```
* --*version* - shows the current version of the *RCML* interpreter, the list of versions supported by the *API* modules, and the build date. If this flag is specified, the interpreter shows the information and immediately exits;
* --*logfile log_file_path* - writes the output of the *RCML* interpreter to a file in the *log_file_path* part;
* *rcml_pc_file* is the path to the file with compiled *RCML* program; and
* *rcml_params* is the startup options of the *RCML* program. Read more about interpreter options in section ["Passing parameters to the RCML program"](http://rcml.info/eng/aboutrcml#paragraph-178).

**Important**! The interpreter automatically appends the .*pc extension to rcml_pc_file*.

***

# 2 RCML Program Architecture Basics

### 2.1 Language Alphabet and the Use of Characters

RCML language alphabet comprises:

1. Symbols used to compile identifiers
   * Latin lowercase or uppercase characters;
   * Arabic numerals 0 to 9;
   * Underscore character «_»;
2. Separator characters:
   * Space character;
   * Tab character;
   * Line break characters;
3. Special symbols – symbols that perform specific functions in construction of various language constructs: + - * / = { } ( ) <> , ; : ~ # @ ! "
4. Composing characters – a group of characters that are seen by the compiler as a unified whole:  ::  ->  ==  >=  <=  !=
5. “Unused” characters – characters that are not included in the set indicated above, but, nevertheless, can be used in comments or to set values of the constants and strings;
6. Reserved words described below.

### 2.2 Rules for Identifier Compilation
The identifier is the name for program elements (functions, variables, etc.) freely selected by the programmer:

* The identifier must begin with a letter or underscore character;
* Numbers can be used in the identifier along with the letters starting from the second position;
* The characters input are case sensitive when specifying identifiers;
* Space is a separator and can not be used inside the identifier;
* Reserved words are not allowed as identifiers.

### 2.3 General Structure of Programs in RCML

*RCML* program consists of two key sections: the section of inclusions and the section of functions.

Example of *RCML* program:
```
// Section of inclusions
include “function.rcml”
include “C:/robot/robot.rcml”

// Section of functions
function sum(a, b) {
	c = a + b;
	return c;
}
function main() {
	s = sum(1, 2);
	echo(“sum = ”,s,”\n”);
}
```
Other *RCML* program files with the text that should be included in this program may be specified in the section of inclusions. It can be sets of any functions, additional libraries, etc. However, the section of inclusions may be empty as well.

The section of functions starts after the first mention of function keyword. Main program code is specified in the section of functions, which consists of functions. 

By analogy with *C* and *C++* programming languages, execution begins with a function named *main*, which must be present. This statement refers to executable *RCML* programs (not libraries). The difference between the library and the executable program will be described later.

### 2.4 Inclusion of Additional RCML Files into the Program

Additional files of *RCML* programs are included as follows: each file to be included is written from a new line. *Include* keyword must be placed in the beginning followed by a path to the file to be included in double quotes through the separating character (for example, space character).

Syntax:
```
include “path_to_file”
```
Examples:
```
include “function.rcml”
include “C:/robot/robot.rcml”
include “../libs/robot.rcml”
```
The path to the file to be included can be absolute or relative. In case of a relative path, the compiler first calculates the absolute path to the file relative to the absolute path to the file, where this inclusion structure is found. If the file is not found by the resulting path, the compiler calculates the absolute path relative to each variant of the path from *path* parameter of *lib_search_paths* section in *config.ini* configuration file in the order in which these paths have been specified in the configuration file.

This path parameter from *lib_search_paths* section of the configuration file is called “default search path”. The syntax for specifying default search paths is as follows:
```
[lib_search_paths]
path = path_1
path = path_2
```
The order of inclusion of additional files with the code is the same in which they have been listed in the original *rcml* file.
```
Important! If non-empty section of inclusions is found in the file to be included, files from this section will be included in the source file immediately after inclusion of the file (right after the line with include word) where they were found.
```
The absolute path is calculated for each included file, and files included again with the same absolute path will not be included in the program.

You should understand a mechanism to include additional files in the program. This is analogous to the fact that instead of *include* structure, the text of the included file is placed in its entirety. The syntax of each file included is checked before inclusion, and the program is compiled taking into account all calculations and optimization only after preparation of the full text of the program considering all the included files.

### 2.5 Including RCML Library Files into the Program

Additional libraries written in *RCML* are included as follows: each library included is written in a new line from *include_lib* keyword in the beginning followed by a separating character (for example, space character), followed by the identifier – library name (to use to apply to the library in the code), and then a separating character and a path to the file with library byte code (pc-file) in double quotes.

Syntax:
```
include_lib library_name “path_to_library_file”
```
Example of the program including math library:
```
include_lib math "../export_library/etalon.rcml.pc"
function main() {
	s = math.sum(1,2);
	system.echo("\n1 + 2 =",s,"\n");
}
```
The process of creating the library is described in Section ["Creating RCML Library File"](http://rcml.info/eng/aboutrcml#paragraph-59).

Library file search process by the compiler is similar to the search of the file to be included. However, the library code is not included into the executable program code. A link to the library file is created in the executable program, and running such a program will also require the file of the library compiled, as in case of compilation.

You can include the same library file under different names-identifiers. 

It should be noted that libraries can have links to other libraries, i.e., when compiling the library, it is allowed to include other libraries in it.

### 2.6 Functions

As noted earlier, the executable program that is not a library must always have a function called *main* (hereinafter the main function). Execution starts from it. It does not matter where the function will be placed: in the source file or in one of the included files. It is important that this function existed and was the only one.

Of course, a programmer can create new functions in addition to *main* function, with names corresponding to the rules for specifying identifiers, and apply to them both in main function, as well as in the others.

New function is specified starting from *function* keyword, followed by the name of the function through the separator, and then the list of parameters in parentheses that can be empty. The list of parameters is followed by a function code – a list of its operators in curly brackets.

Syntax for specifying a function:
```
function function_name(list_of_parameters) {
	list_of_operators
}
```
Example function:
```
function sum(a, b) {
	c = a + b;
	returnc;
}
```
A function in *RCML* language can either return a value or not return it. Values are returned (similar to exit from function) using *return* operator. 

It is optional to specify this operator it the end of the function if the value is not returned. If the function does not return the value, then by default the return value is considered to be zero – 0.0.

Function parameter values are always transferred by value, so they can be used as regular variables in a function body without fear of overwriting data in the function where the current one was called from.

The order of the functions with respect to each other is not important and does not affect the scope of their visibility. Each function can be called from any other function, except for the main function. Since execution begins from it, it cannot be called in the code of functions. It is worth noting that a function cannot be declared within the function.

The parameters in the main function *(main)* have a special meaning, see more in Section ["Passing parameters to the RCML program"](http://rcml.info/eng/aboutrcml#paragraph-178)

### 2.7 Comments

Single- and multi-line comments are available in *RCML*. Single-line comment begins with double forward slash // and follows until the end of the line.

Example:
```
//This is a comment
```
Multi-line comments begin with a combination of characters /* and continues to the following combination of characters */

Example:
```
/* This
is a multi-line
comment */
```
### 2.8 Data Types

Explicit and implicit data types are available in *RCML*. The first group includes those data types with variables which can be created and used in various operations. The second group, on the contrary, includes variables which can not be created, but these data types may appear in some implicit operations, or they can be used as parameters for specific functions. Data type for connection with the physical robot is set aside from this classification.

It should be noted, that at this stage of *RCML* development, other data types, including arrays are not yet provided.

### 2.8.1 Explicit Data Types

There is only one explicit data type in *RCML* – the real numbers. All generated variables used in the operations, parameters and return values of user-defined functions, and also of all functions of modules and robots are the real numbers.

### 2.8.2 Implicit Data Types

Logical and string types are implicit data types in *RCML* language.

Data used in calculation of logical expressions are forcibly converted to the logical data type.

String data type is used only to specify string constants that can be parameters for the system-level functions (RCML system functions, functions of robot modules and functional modules). A string constant is a sequence of any characters enclosed in double quotes, while the following control character sets can be specified in a string constant, deemed a single character:

* \n – word wrap;
* \” – inserting the double quote character;
* \ \ - inserting a backslash.

### 2.9 Variables

A variable in *RCML* language is initialized when you first assign it a value, and it remains visible from the point of initialization to the end of the function body where it is used. A single character “=” is used to assign a value to a variable. Constant values, results of expressions and function calls can be assigned to variables. A variable can be transferred as argument to the function. Variables that are the parameters of the function are initialized when transferring of execution to this function.

Example of using variables in a function:
```
function sum_by_abs(a, b) {
	c = a + b;
	if (c < 0) {
		с = -с;
	}
	return c;
}
```
There are no global variables in *RCML*.

The programmer may not take care of memory consumption, and may not delete the variables used when they are no longer needed. The compiler takes care of it in optimizing the resulting byte code, calculating the life paths of each variable. However, this does not apply to specific variables related to the physical robots.

### 2.10 Expressions and Operations

Expressions in *RCML* can consist of numeric constants, variables and returned function results that can be used as operands of the mathematical and logical operations as part of the expression. Operations, in turn, may be unary or binary. Priority of operations in expressions is given in parentheses, just as it is in mathematics. The result of the expression can be assigned to a variable or transferred as a parameter to a function. It is possible for those function parameters that work with these data types. Using special variables to communicate with a robot in the context of expressions is prohibited at the level of syntax rules.

Example expressions:
```
a = 1 + 1;
a – 5;
c = a * b;
d = sum(a, с) * 10 * (a / c);
e = -d + 15;
f = d >= 10;
!f;
```
### 2.10.1 Mathematical Operations

The following binary mathematical operations are available in *RCML*:

* «+» - addition operator;
* «-» - subtraction operator;
* «*» - multiplication operator;
*  «/» - simple division operator;
* «%» - taking remainder of division operator.

And one unary operation:

* «-» - sign change.

### 2.10.2 Logical Operations
The following binary operations from logical operations are available in *RCML*:

* «==» - checking equality;
* «!=» - checking inequality;
* «>=» - checking greater than or equal to;
* «<=» - checking less than or equal to;
* «>» - checking greater than;
* «<» - checking less than;
* «&&» - conjunction, AND operation;
* «||» - disjunction, OR operation.

And one unary operation:

* «!» - logical negation.

### 2.11 Converting Data Types

By using different types of operations in one expression, expression result data type is implicitly converted to the expected type of the operand of the next operation. The result will be real if it is saved in a variable or transferred as a function parameter, or strictly logical if it is used in a conditional operator.

The following rule applies when converting real type into logical type: 

*if the element is not zero, then the result is true, otherwise – false*. 

The following rule applies in reverse conversion:

*if true, the result is 1.0, if false, the result is 0.0*.

### 2.12 Control Structures

*RCML* provides a standard set of structures affecting the progress of the program execution. They can be divided into conditional, cyclic and unconditional.

### 2.12.1 Conditional Control Structures

*RCML* has only one conditional transfer operator – *if*. Its syntax is as follows:
```
if (expression) {
	//set of operators, if the result of expression is true
} else {
	// set of operators, if the result of expression is false
}
```
The result of expression in parentheses is reduced to a logical type, and if the result is true, then the first block of operators in curly brackets is executed, otherwise – the second block of operators following *else* keyword. The second block of operators with *else* keyword can be omitted when specifying a conditional operator.

### 2.12.2 Cyclic Control Structures

Cycle (loop) operator in *RCML* is also unique. It is loop operator. It is a simplified cycle operator in comparison with similar operators in traditional programming languages. It is unconditional, and its block of operators will be executed endlessly, unless it has an appropriate exit structure with prerequisite or without it. The syntax of loop operator is as follows:
```
loop {
	// set of loop operators
}
```
### 2.12.3 Other Control Structures

Other control structures in *RCML* are operators of unconditional transfer. When reaching them, the program goes to a particular section.

The operator of transfer to the beginning of the cycle – *continue*. When it is reached, the program goes to the beginning of the current cycle. It is used without parameters.

The operator of loop exit – *break*. When it is reached, the current loop is interrupted. It is used without parameters.

The operator of function exit – *return*. It may be used both with an expression, and without it. In the first case the result of the function will be the result returned by the expression. In the second case, the function will return the default value of 0.0.

The syntax of *return* operator with expression return:
```
return expression;
```
Without expression return:
```
return;
```
Examples of return operator:
```
return 2+2; //function exit with expression return
return; // function exit without expression return
```
If a *return* operator is specified in the *main* function, its implementation will lead to termination of the RCML program, and the value passed via this operator will be passed to the OS as the program exit status. However, the passed value will be rounded to integer by dropping the fraction part.

The operator exiting the program – *exit*. When it is reached, the program stops executiong regardless of function where it is specified. This operator (like *return*) may be called both with an expression, and without it. In the first case, the result of the expression is returned to the OS as the exit code of the program, in the second case the exit code of the program is considered to be equal to 0.

The syntax of exit operator with expression return:
```
exit expression;
```
Without expression return:
```
exit;
```
### 2.13 Exceptions

In *RCML*, it is available to handle exceptions occurring in a particular block of code, as in some other programming languages. General type of exception handling structure begins with *try* operator, then it is followed by a block of operators, where an exception can occur in execution of the operators of this block, then it can be followed by catch operator, and a block of operators to be executed in case of an exception. General type of exception handling structure is as follows:
```
try {
	//block of operators, where an exception can occur
} catch {
	//block of operators to handle the case with an exception
}
```
When an exception appears in *try* block, the program goes to catch block operator. It should be noted that *catch* operator and its block may be omitted, and then the program goes to the operator following the block of *try* operator. Exception handling structure may be nested in another one, and, in case of an exception, it will be handled by the current structure.

Exceptions may occur when executing various functions provided by external or system *RCML* modules, such as function modules or robot modules, but an exception can be called manually using throw operator. When executing this operator, an exception will immediately be thrown.

Work of the mechanism of exceptions as an example of a simple program for division of numbers:
```
function main() {
	try {
		echo(“this simple division program\n”);
		echo(“c = a / b \n”);
		try {
			echo(“input a\n”);
			a = input();
			echo(“input b\n”);
			b = input();
			if (!b) { // if b is zero, division cannot be executed
				throw; //throw an exception
			}
			c = a / b;
			echo(“c = ”, c, “\n”);
		} catch {
			echo(“b is zero!”); //exception will be handled in this block
		}
	} catch {
		//this block will never be executed
	}
}
```
However, in *RCML*, focused on robotics, unlike other programming languages, *try* operator can have parameters that specify exactly how it should work. The first parameter of *try* operator is a string constant specifying the mode of operation. The second parameter of real data type may be given depending on the specified mode.

Try operator has three modes of operation:

* “error_default” – default mode of operation as a common *try* operator. In this case, the second parameter is not specified. If the parameters of try operator are omitted as in the above example, then *try* operator operates in this mode.
* “error_time_limit” – mode of operation with a countdown of time limit in which a block of code of *try* operator should be executed. In this case, the second parameter is given to specify the number of milliseconds limiting execution of the block of code of *try* operator. If this block is not executed within the specified time, an exception will be thrown. If an exception is thrown earlier, the countdown will be stopped, and the exception will be handled normally.
* “error_try_count” – mode of operation with a count of the number of attempts to execute the block of *try* operator. In this mode, the second parameter takes a number of allowed attempts to execute the block. In each thrown exception, the count of the number of attempts in *try* operator block will be reduced by 1, and if it reaches zero, the exception will be handled normally.

Despite the fact that try operator may take the parameters, it is not a function and does not returns a value.

Example of using the above modes to handle success of robot performance of its function giving it three attempts with a time limit of 2 seconds for each:
```
try(“error_try_count”, 3) {
	try(“error_time_limit”, 2000) {
		robot->do_something();
	} catch { //if time is out
		throw; //then throw an exception to end the attempt
	}
} catch {
	//this block is executed when all attempts are ended 
	//with no result obtained
}
```
Using the *throw* operator, the exception may be used for passing some value (exception value). In this case, the syntax of the *throw* operator will be the following:
```
throw expression;
```
For processing the value of the thrown exception, the try operator will have a slightly different syntax: the parameters of the *catch* operator should specify the identifier, i.e., the name of the variable to which the exception will be written:
```
try {
	//the block of operators in which the exception may occur
} catch (variable_name) {
	//the block of operators for handling an exception
}
```
If at the time of exception the specified variable does not exist, it will be created, otherwise it will be overwritten.
```
try {
	throw 3;
} catch (E) {
	system.echo(“E = ”, E, “\n”); //will show E = 3
}
```
The variable, to which the value of the exception will be written, will be available in the *catch* block and beyond it till the end of the function.

An example of creating the variable:
```
system.echo(“E = ”, E, “\n”); //error E does not exist yet
try {
	throw 3;
} catch (E) {
	system.echo(“E = ”, E, “\n”); //will show E = 3
}
system.echo(“E = ”, E, “\n”); //will show E = 3, since E has been created in the catch block
```
An example of overwriting the value:
```
E = 5;
system.echo(“E = ”, E, “\n”); //will show E = 5
try {
	throw 3;
} catch (E) {
	system.echo(“E = ”, E, “\n”); //will show E = 3
}
system.echo(“E = ”, E, “\n”); //will show E = 3
```
In this way it is possible not only to process exception values passed via the *throw* operator, but also to process exception values passed from functions of robot modules and functional modules, and libraries.

Example:
```
try {
	robot_test->throw_value(10);
} catch (E) {
	system.echo(“E = ”, E, “\n”); //will show E = 10
}
```
If the thrown exception has not been intercepted in the RCML program, the RCML program will terminate with code 1, i.e., an error, which may be a communication tool between the OS and the RCML program (read more in Section ["Communication with the OS"](http://rcml.info/eng/aboutrcml#paragraph-58)). In this case, the value of the exception will be lost. 

### 2.14 Calling Functions

*RCML* language functions can be internal or external.

Internal functions refer to the functions described in *RCML* language in the current file of program source code or in included files with the source code in *RCML*. The code of these functions is within the current program and will be compiled into an executable file of the current program.

External functions refer to the functions of *RCML* libraries already compiled, linked to the current program or the functions of functional modules, i.e., the code of these functions is outside the current program in *RCML*.

Syntactically, internal functions in RCML are called in the same manner as in other programming languages. You must first specify an identifier – the function name, and then a list of arguments given to it in parentheses:
```
identifier(argument1, argument2, argument3)
```
The result of any function call can be assigned to a variable or used in the expression. It should be noted that in the function call, all parameters specified in its description should be transferred through arguments, except for some *RCML* system functions.

Calling an external function has a different syntax:
```
source_name.function_name(argument1, argument2, argument3)
```
An identifier (name) of the library linked from the section of inclusions is used as the source name, or the name of the function module specified in the configuration file. Description of names and arguments of external functions can usually be found in the documentation for the respective source – the library or function module.

It should be noted that when searching for the external function, the desired source is in the first place sought among linked libraries and then among linked functional modules. If the source is found, the desired function is sought in it by name, if the function is found, the number of function parameters declared in the source is checked against the number of arguments transferred by the user. If the number of arguments and parameters is the same, it is believed that the desired function is found. In any other case, an error is issued.

*RCML* has *system* module, always available in the program and providing system functions of *RCML* language. If this name is specified as the source of external functions, the function is searched directly in this module, ignoring the libraries with the same name.

### 2.15 System Functions in RCML

System functions in *RCML* are available in *RCML* environment via *system* module. These functions are accessed in the same way as the external functions of the module, specifying “system” name.

The system module has the following functions:

* system.input – a function of reading a number from a string resulting through standard input. The line read should consist of digits and optionally of the sign + or -. Then, it is converted to a number, which is returned as a result of function call. This function has no parameters.
* system.echo – a function of data output transferred as arguments to the standard output. It can have any variable number of parameters including the numbers and string constants.
* system.set – a function of change in RCML environment system parameters. It has two arguments: the name of a system parameter and its new value. The name of a system parameter is defined by a string constant, and the new value of this parameter depends on the system parameter specified. Now there is only one system parameter in RCML environment:
  * “behavior” – function execution behavior by default. The second parameter is the mode flag, character ~ or #. See Section ["Batch Data Transmission to Robots"](http://rcml.info/eng/aboutrcml#paragraph-57) for details about execution modes.
* system.sleep – a temporary pause in the program execution process. It has one numeric parameter – the pause time in milliseconds.
* system.hand_control – enabling manual control for a robot. This function transfers the robot in hand control mode by the said control module. Execution of the main program for the period of hand control is suspended. This function has a variable number of parameters:
  * First parameter – a special variable associated with the appropriate physical robot. Mandatory parameter;
  * Second parameter – a string constant – control module name. Mandatory parameter;
  * Third parameter – a string constant - robot axis;
  * Fourth parameter – the name of control device axis as a string constant or an expression that will set the values for robot axis specified in the previous parameter.
  * Fifth and sixth, seventh and eighth, etc. parameters are always set in pairs: robot axis and the source of values for it.

* system.send_package – a command to send the accumulated command package to robots. The only parameter is the flag for the mode of execution of commands from the package, ~ or #. Read more about this function in Section ["Specifying Function Execution Modes"](http://rcml.info/eng/aboutrcml#paragraph-55).

### 2.16 Macros

*RCML* supports macros. A macro is a combination of an identifier – macro name – random text –macro contents. The macros are declared in the section of declarations by *define* keyword, followed by a separator, followed by an identifier (macro name), a separator again and macro text.

Macro syntax:
```
define macro_name macro_contents
```
All occurrences of the macro name in the text of the section of functions will be replaced by its contents. After defining a macro can not be re-defined by other macros.

The macro content includes any text between the separator character following the macro name and the line end. Macros can have multi-line content (use «\» character, i.e., shield the line end, as it is done in multi-line macros in *C++*). Then the macro content will be considered as the text until the end of the line following the line ending with this character. Line feed characters in multi-line macros are preserved.

Example use of macros
```
define ONE 1
define TWO 2
define TREE ONE + \
TWO
define TEST_MS "test message"

function main(){
	system.echo("1 = ",ONE,"\n");
	system.echo("2 = ",TWO,"\n");
	system.echo("3 = ",TREE,"\n");
	system.echo("Test print > ",TEST_MS,"\n");
}
```
This text after macros processing will be converted to the following text:
```
function main(){
	system.echo("1 = ",1,"\n");
	system.echo("2 = ",2,"\n");
	system.echo("3 = ",1 + 
2 ,"\n");
	system.echo("Test print > ","test message","\n");
}
```
The resulting text will already be compiled into the byte code. The result of this program will be as follows:
```
1 = 1.000000
2 = 2.000000
3 = 3.000000
Test print > test message
```
```
Important! It is dangerous to use macros, which recursively refer to each other. In this case, an infinite recursion occurs, which will cause the compiler crash. At this stage of language development, closed recursion in macros cannot be found.
```
Example closed recursion:
```
define ONE TWO
define TWO ONE
```

# 3 Features of Program Interaction with Robot
As noted above, *RCML* language is focused on robotics and has rather poor components as a programming language, since it is not intended to create general-purpose application software and is aimed at interaction with robotics, allowing to achieve new results in this regard.

### 3.1 Robot Concept in RCML
A robot in *RCML* is a kind of executive resource that can be engaged to perform a specific task (function) and then released for re-engagement, but in another task for example. The immediate analogy is processing core of *CPU*, which can be engaged in only one process at a particular time. However, *OS* means allocating very small quanta of *CPU* time to different software processes or tasks, can create the illusion that one core handles multiple tasks. In *RCML*, the robot as a resource is controlled by the interpreter, but the robot is engaged not for a period of time, but to perform a specific function or multiple functions, and thus the time of its engagement may be different.

A robot is an object of the real material world, and its actions are often not as instantaneous as in the above analogy with the *CPU* core. And it takes time to perform its function, and this time may be different for the same function as a robot is affected by many factors of material object world.

A robot module as such provides RCML environment with robot class description assigned to it. It is assumed that locally in the object world where RCML is used, maybe one or more robots of the same class assigned to one robot module. And there are two key types of objects as part of the robot module in RCML environment, as shown in Figure 4:

* Robot module acts as a controller for robots of the class entrusted to it, i.e. selects a free robot to be engaged and released after work;
* Robot representation – a reflection of a specific physical robot in a virtual RCML environment, translating commands to the physical robot.

![](http://rcml.info/images/aboutrcml/4bb902fa8fd981bcdd2f7bf654681a36.jpg)

Figure 4 Virtual representation of objects classes in RCML environment

It should be noted that many robot classes may be connected simultaneously, i.e. many robot modules, and each of them can provide access to multiple robots in its class. Robots within the same class must be completely identical, both in physical and functional performance.

### 3.2 Using Robots in the Program

For the robot to be used in the program, its class and function should be specified. The class name of the robot coincides with the name of the robot module in the config.ini file. However, the class of the robot in the *RCML* program should be specified using keyword robot and an underscore. 

The syntax for activating the robot:
```
robot_class_of_robot
````
For example, you can call the robot from module test, and then its class will be identified as:
```
robot_test
```
Meeting the class name of the robot in the text of the program, the system will send a request to the appropriate module of the robot and will stop program execution until it finds a free robot of the required class.

### 3.3 Calling Robot Function

After receiving the robot of the required class, the programmer can make him perform the requested action, i.e., cause the execution of a certain robot function. Robot functions are programmed by the robot developer together with robot module and are described in the documentation to robot module.

Calling a function is visually similar to the object method call in *C*-like programming languages. Robot class must be specified, and then through the pointer character “->” the required function is specified, and then goes the list of arguments to this function in parentheses. 

Robot function call syntax:
```
robot_robot_class->robot_function(arguments);
```
For example, you should call do_something function with one argument 1000 from *test* class robot:
```
robot_test->do_something(1000);
```
Reaching such structure, the interpreter will reserve a robot of this class, waits until the real physical robot is engaged and then sends a command of robot representation of the execution of the specified function with the specified parameters. After function execution, the robot will be automatically released and transferred to free status.

It should be noted that when robot function call is specified this way, the interpreter waits for confirmation of the function from robot representation, and only then continues executing the rest of the program. Read more information about flags of function execution in Section ["Specifying Function Execution Modes"](http://rcml.info/eng/aboutrcml#paragraph-55).

Depending on how and where the execution of robot functions is described, they (functions) can be divided into three types:

* User-defined functions – the functions described in RCML language in the current file of the source program code or in the included files with the source code in RCML. The code of these functions is within the current program and will be compiled into an executable file of the current program;
* Library functions – the functions of RCML libraries already compiled, linked to the current program, i.e., the code of these functions is outside the current program in RCML;
* System functions – the functions that are provided to RCML environment by robot module. The execution code of these functions is in robot module or in robot software.

System function can be re-defined by user-defined and library function. Library function can be re-defined by user-defined function only. User-defined function can not be re-defined. When you create two user-defined functions with the same name, the compiler will generate an error. This mechanism allows you to re-define inefficient functions of the level, where it is more difficult to do, to the functions of the level, where it is easier to do. For example, it may be difficult to recompile robot module or library due to the absence of source codes, however, it is easy to re-define the required function in the text of your program.

Thus, in a function call, the specified robot function is first searched among user-defined functions. In case the required function is not found among user-defined functions, the search continues among library functions. Each library is searched for the required function in the order it is included in the source program code. It should be noted that different libraries may include th same function for the same robot, so the first function found will be selected. If the required function is also not found among library functions, the search continues among system functions. There may be multiple system functions with the same name as part of robot module, but the first function found is always selected. If the required function is not found among system functions, then an error is generated.

The function is searched only by its name without taking into account the number of its parameters. If the function found has a number of parameters other than the number of arguments sent in the function call, an error is generated.

Based on this search algorithm, it can be concluded that user-defined functions have a highest priority in the search, followed by library and then system functions.

However, sometimes a programmer faces the need to use system function, which is re-defined by library function. This case is possible when library function implementation is inefficient, the source code of the library is absent, and there is the need to use this library because it includes other functions. It is possible to directly access system function if you include a dot character “.” before the function name. Function call syntax is as follows:
```
robot_robot_class->.robot_function(arguments);
```
the above example is as follows:
```
robot_test->.do_something(1000);
```
### 3.4 Working with a Certain Robot

It is often necessary to call multiple robot functions, and they must be performed by a single robot as a predetermined sequence of actions. If the required sequence of functions is called as described above, in case of robots of several classes it is likely that the specified functions will be performed by different robots. If there is a single robot of a given class available, it every time will be engaged and released for each function.

The most effective and rational is to engage a robot once and send commands to it as necessary and then release it, thus implementing a session of robot operation. This requires engaging a robot of the required class and remembering connection to a specific robot engaged. This can be done by saving the robot in a special type of variables with *“@”* character before the identifier. For example, we engage the robot of test class, and save connection with the specific robot in *@r* variable:
```
@r = robot_test;
```
Reaching such a structure, the interpreter, as in case of a robot function call, will send a request to engage the robot, but now the robot will not be released until the command to release this certain robot is reached.

Now, to call the function of this engaged robot, we need to call the function by applying to this variable, not to the robot class. For example, calling the same *do_something* function of this robot with the same parameters:
```
@r->do_something(1000);
```
After performing this function, the robot will remain engaged, and we can call the next function of this robot.

It is not allowed to use specific variables with *@* character in the context of *RCML* expressions. They can not participate in operations, comparisons and be transferred as arguments to functions (except for *hand_control* system function). However, a robot can be assigned to another variable of the same type, i.e. communication with the engaged robot can be saved in several variables, for example:
```
@r = robot_test;
@r2 = @r;
@r4 = @r3 = @r2;
```
As a result, all variables *@r, @r2, @r3* and *@r4* will be attributed to the same robot.

The variable already attributed to a specific robot can be assigned with a pointer to a completely different robot, or even a different robot class:
```
@r = robot_test;
@r2 = @r;
@r = robot_tarakan;
```
Now *@r* is attributed to a robot of tarakan class, and *@r*2 is attributed to a robot of test class. Note that when you change the robot which the variable is attributed to, the robot which it was attributed to previously, will not be released.

### 3.5 Releasing the Engaged Robot

A robot “saved in the special variable” can be released using a special delete operator, when it is required. This operator must be accompanied by a special variable that holds a pointer to the robot to be released. Example of robot release with a pointer previously assigned to a variable *@r* is as follows:
```
delete @r;
```
It should be noted that all robots involved and not released using delete operator, will be released at the end of the function execution only, where they were engaged. This statement does not apply to robot functions written in RCML, because these functions are executed in the context of a robot instance, and a robot instance be engaged in them. Read more about these functions in [Section "Writing Own New Functions for Robots"](http://rcml.info/eng/aboutrcml#paragraph-54). 

**Remember that, by analogy with traditional programming languages where the programmer can allow a memory leak, you can now allow a robot leak in RCML language**.

[Section "Features of Function Execution by Robots in Different Modes"](http://rcml.info/eng/aboutrcml#paragraph-56) also contains important additional information about the mechanism to release the robot engaged.

### 3.6 Automatic Robot Selection

One of *RCML* features is automatic robot selection for a certain task. To use this feature, just specify robot keyword instead of a specific robot class where it is required to specify robot class: robot function call or assignment a robot to a variable. For example:
```
robot->do_something(1000);
@r = robot;
@r->do_something();
```
Using only robot keyword instead of the full robot class name will be further called the abstract robot.

When using the abstract robot in a function call, *RCML* environment will automatically check what robot types from robots available in the system have this function with this number of parameters, and, if they are available, it will generate a list of candidate robot classes that can be used in the function call. Thus, not only different robots within the same class can be engaged in this function call, but also robots different by classes.

In case of using a special variable for communication with the abstract robot, *RCML* environment analyzes each function call with respect to this variable, and makes a list of candidates only of those robot types which have all called functions with respect to this variable.

### 3.7 Using the robot choosing module
In the method of robot activation described in section ["Using Robots in the Program"](http://rcml.info/eng/aboutrcml#paragraph-168), specific physical robot, or rather the associated robot representation is chosen by the robot module, which may know nothing about the context of using the activated robot, and therefore it is likely to make not the best choice of the robot. Most often, the first available robot within a specific module of robots is called this way if a specific required class of robots had been specified. Or, if an abstract robot had been called, it would be the first free robot from the first module of robots with unused robots, in the order in which the modules of the robots are listed in theconfig.ini file.

If in the process of the RCML interpreter operation the option to record statistics (see section ["Installation and Configuration of RCML Compiler and Interpreter"](http://rcml.info/eng/aboutrcml#paragraph-146)) was activated, the statistical information about robots operation and the speed of executing their functions is collected. For more about data collection, see Section ["Working with RCML statistics"](http://rcml.info/eng/aboutrcml#paragraph-67). The robot choosing modules may have access to this information, and, based on it, can make a more rational decision about the choice of the robot.

Connecting one or several choosing modules is specified in the robot manipulation the operator in angle brackets <>, where all choosing modules are listed, comma-separated. The syntax of this construction is as follows:
```
robot_class_of_robot<list_of_choice_modules>
```
An example of connecting choosing module *avg* to choosing a *tarakan* class robot:
```
robot_tarakan<avg>
```
In this case, the *tarakan* robot module will request all physical representations of the available robots, and their list will be passed to choosing module *avg*. From the provided list, the choosing module will choose the most appropriate robot, which will be used. The choice algorithm is determined by the choosing module.

It should be noted that choosing a robot using a choosing module usually takes longer than without the one, since the module has to perform additional actions associated with decision-making (statistics database queries, etc.).

If the robot chosen by the choosing module has become engaged in another process, the choice procedure starts again, querying the robot module for available robots.

The choosing module may be also indicated in case of using an abstract robot. For example, indication of statistics module *avg*, with abstract robot:
```
robot<avg>
```
In this case, *RCML* get a list of all presentations of the available robots from all robot modules matching this call. From the list of robots representations, only one robot of a particular class will be chosen.

In case several statistics modules were indicated for robot initialization. For example: 
```
robot_tarakan<test1, test2, test3>
```
In this case, every choosing module will get a list of representations of the available robots, out of which each module will choose one robot. In this case, the order of modules indication determines the sequence of decision-making by the modules, as well as the priority of the robot chosen by a particular choosing module, which may be expressed in points. The first specified choosing module will be the first to select a robot, and the robot selected by it will have the maximum priority in points that will be equal to the number of the specified choosing modules. Accordingly, the last specified module will be the last to make decision, and the robot it chooses will have the priority equal to one point. If the choosing modules choose for activation different robots from the list of the available robots, the robot with the highest points will be activated, i.e. the robot with the highest priority. If the robots chosen for activation have the same points, the robot chosen by the first choosing module will be selected. 

It is important to note that a situation is possible where a specific statistics module for any reason cannot make the decision about choosing a robot. In this case, the module notifies the *RCML* environment, and environment's current behavior is determined as though this module has not been specified for robot activation.
***
# 4 Hand Control Mode
### 4.1 General Information
*RCML* environment can provide capability of hand robot control by means of a certain control device when calling *hand_control* system function with the appropriate parameters.

The basic operating principle of *RCML* environment when switching to hand control mode is to match robot axes along which it can somehow move, with control device axes along which this device can detect changes, see Figure 5. 

![](http://rcml.info/images/aboutrcml/b5f4ec3cd0cd9a3e308ae6e0b2e2a69d.png)

Figure 5 Example robot axes and control device axes

This example includes a robot crawler (shown to the left), which can move into its new absolute position of the plane through a series of changes in its position along two axes: *R* axes of movement (forward or reverse) and A axis of rotation A (left or right). And there is a simple control device (a joystick) (shown to the right) which can be deflected in the plane from its initial position along two axes – *X* and *Y*. Accordingly, by means of *RCML*, you can relate joystick and robot axes so that joystick deflection results in robot movement. For example, joystick deflection along Y axis in the positive direction causes movement forward, and joystick deflection along X axis in the negative direction causes robot turn to the left. Let’s assume that this robot is specified in *RCML* environment with tarakan module, and the joystick, respectively, with joy control module. *RCML* code to link them in hand control mode to obtain the above effect, is as follows:
```
@r = robot_tarakan;
hand_control(@r, “joy”, “R”, “Y”, “A”, “Y”);
```
### 4.2 Value Transfer Principle

Thus, control device axis is a source of values, and robot axis is a receiver, and the robot itself is the executive device. Of course, the scale of values or ranges of robot and control device axes may not coincide. *RCML* environment automatically brings the values from control device axis to the range of the values of robot axes, maintaining their ratio.

*RCML* environment gets information on robot axes and the ranges of their values through *API* from robot module, and thus they must be declared to the documentation for this module, in order for *RCML* programmers to use them. Similarly for control devices.

This principle can be extended not only to real axis of movement of a robot or a joystick, but to all its functions, which anyhow can have values and change robot status in real time (within the OS capabilities) for example, turning on light indicators, blocking robot movements, etc. The only requirement is that they must be declared in robot module and documentation to it.

Additionally, you can create the so-called discrete axes, which take a small range of values, and each value may correspond to some status of the robot or its mechanism. A special case is – “binary” axes producing or having a value of 0 or 1, similar to “on” or “off”. A striking example may be an ordinary computer keyboard with 101 keys and, respectively, 101 binary axes. In case of a robot, such axis can be switching on of lamps and lights, or making some sound or enabling voice communication mode. Variations are limited only by imagination of designers of robots and control devices, as well as programmers in RCML, which can link the axes of certain devices in any order. Due to the fact that values can be brought to integers, it is possible to link binary axes of both robot and control device with “non-binary” axes of a certain device.

It is important to note that *RCML* environment has no control over the frequency and variability of transfer of values from control device to the robot. If control device each time sends axis value, then even if it has not changed, this value will be transferred to the robot each time. Module developers are recommended to take this factor into account. In certain cases, developers of modules for control devices should pay attention to the factor of value transfer frequency, because each transfer reaches the robot, and it should have time to respond. Developers of modules for robots are recommended not to lose sight of the fact that it is possible to transfer a series of the same values for a given axis only because it (the series) was transferred by control device.

### 4.3 Recommendations on Robot Axis Selection for Hand Control Mode

This section is addressed to developers of robots and robot modules for RCML environment. When selecting robot axes to control, one should remember of the so-called “alpha-5” problem true for industrial robots using six-axis kinematics, when a minor robot movement in Cartesian coordinates is accompanied by a significant movement in the coordinates of motor axes. And often it is true for the fifth axis of robot motor, whence the name. However, the principle of this problem can be extended to a fairly wide range of robots, including even such a simple robot crawler as described above.

This principle states that a minor change in the target value of the axis along which the robot must take a new position, should cause proportional small changes in the axes of rotation of robot motors and units, possibly causing only one phase of operation of motors involved in one direction, that is, the same motor does not re-accelerate and re-decelerate.

Otherwise, abrupt accelerations of robot parts and mechanisms are possible, and small changes suggest greater frequency of their occurrence and ultimately it can cause excessive wear and even robot failure or damage to objects of its environment and people.

In the above crawler robot example, this principle is observed. However, if we violate it, and for robot axes select, for example, standard Cartesian X and Y axes describing the plane in which the robot moves (see Figure 6), we can get a number of negative effects.

![](http://rcml.info/images/aboutrcml/8429e771123af36df59a4cdd05072945.png)

Figure 6 Example of robot axes selection

For example, a minor positive change in X axis in the current position would require the robot mechanics first to turn the robot to the right for a quarter turn and then move it forward. Each motor would have to perform two operation sessions, one for turn and one for movement, and right track motor would have to change the direction of rotation after the turn, which significantly increases the time when the robot executes the command received and, therefore, reduces robot response speed.

Additionally, selection of such axes of motion for the robot leads to uncertainty, since the robot can move to the new position in different paths:

* Make a quarter turn right and move forward;
* Make a three-quarter turn left and move backward.

In this case, the path selection algorithm is required for the robot, which should be executed whenever a new axis value is received, and each time consuming portion of the CPU time, increasing the robot response speed.
***
# 5 Writing Own New Functions for Robots

Writing own functions for the existing robots is one of the features of *RCML* language. However, it should be noted that according to the canons of programming, new functions should be based on those provided by robot developer through robot module for *RCML* language. This feature is very useful for standardizing names and parameters of the same functions and robots for its subsequent use through the abstract robot. And if robot developer foreseeingly left the access to low-level functions of the robot through robot module, it is possible to expand high-level robot functionality.

### 5.1 Writing Functions for a Certain Robot Class

Specifying a new robot function starts with function keyword, followed by robot class name for that function after the separator, followed by “::” character, followed by the name of the new function and then the list of parameters in parentheses that can be empty. The list of parameters in curly brackets is followed by function code – a list of its operators. The syntax for specifying a robot function:
```
function robot_class::function_name(parameter_list) {
	operator_list
}
```
Example of a new *sum* function for the type of test class robot:
```
function robot_test::sum(a, b) {
	c = a + b;
	robot->do_something(c);
	returnc;
}
```
Specifying a robot function is very similar to specifying a common function in *RCML*, but the execution of such function has a number of significant differences.

The key difference is that a robot function is executed in the context of a particular robot instance and its class. Therefore it is impossible to access the other robots and thus the use of robot classes is prohibited. To access the robot for the function executed, the abstract robot is used (third line of the above example). Of course, all robot functions called via the abstract robot as part of this function must be present in this robot class, that is, they must be provided by robot module, or they must be described in the libraries linked, or in other functions in the source code of the program.

In case there is no access to the robot (attributed to the function) in this function, a real physical robot of the specified class will still be reserved for this function.

Additionally, as part of the robot functions, it is prohibited to use special variables because they are useless, as a robot attributed to the function is always available through the access to the abstract robot.

In the robot function, you can change modes of execution of other robot functions by conventional methods. For more information about modes of function execution and methods to change them, see [Section "Writing Functions for a Certain Robot Class"](http://rcml.info/eng/aboutrcml#paragraph-55).

### 5.2 Writing Functions for Multiple Robot Classes

Specifying a new function simultaneously for multiple robot classes is similar to specifying a new function for a single robot, except that the required robot classes are separated by commas. Example of specifying such a function simultaneously for two robot classes – *test* and *tarakan* – is as follows:
```
function robot_test, robot_tarakan::sum(a, b) {…
```
Robot classes given for this function must include all functions called through the abstract robot within this function, otherwise this new function is contradictory and inapplicable to a specified set of robot classes.
***
# 6 Specifying Function Execution Modes
Functions written in RCML, may be executed in two basic modes:

* Waiting to execute the function – in this case, after reaching a function call command, the interpreter just goes to the beginning of the function called and consistently executes its code;
* Not waiting to execute the function – in this case, a subsidiary thread runs that starts to execute the code of the function called. Execution of the function where the call was generated, continues from position following the call.

In case of “Not waiting to execute the function”, the thread generated can be transferred to a separate processing core by the OS means, and thus causing the effect of parallel *RCML* code execution.

By default, all functions are called in “Waiting to execute the function” mode. This mode is the default mode. You can change function execution mode in several ways.

The first method includes the use of mode flags. Each of them is one character. There are always two characters as the types of modes:

* #- A flag of function execution with waiting;
* ~ - A flag of function execution without waiting.
A mode flag must be specified at the function call before the function name. Examples of use:

A mode flag must be specified at the function call before the function name. Examples of use:
```
~do_something(1000);
#do_anything(1000);
```
The second way to change the mode of the function execution is to use set system function specifying *“behavior”* string constant as the first parameter, and the mode flag # or ~ as the second parameter. Calling this function with these parameters re-defines the default function execution mode, that is, if the mode flag is not explicitly specified in the function call, the function will be executed in the mode specified by the second parameter of set function. Example of set function use:
```
set(“behavior”,~);
//all subsequent function calls will be executed
//without waiting for completion
do_something(1000);
do_something(1000);
do_something(1000);
//mode does not change, because the flag is the same as the default value
~do_something(1000);
//explicit mode change, but only for this particular function call
#do_something(1000);
```
It should be noted that a function can not be called in a mode without waiting for execution, if the result of its call is used anywhere – for example, is saved in a variable or is involved in the expression. In this case such function will be forcibly called in a mode with waiting for execution, regardless of the default mode settings. Placing a mode flag before the function call with used result is prohibited at the level of syntax rules, i.e., in attempt to execute the below example, the compiler generates an error:
```
function sum(a, b) {
	return a+b;
}
function main() {
r = ~sum(1,2);
}
```
System functions (from system module) except send_package function, which will be described in detail in the below [Section "Features of Function Execution by Robots in Different Modes"](http://rcml.info/eng/aboutrcml#paragraph-56), are always executed in “Waiting to execute” mode and ignoring the mode flags before the function call.

If some function is called without waiting to execute, i.e., becomes executed in parallel, the values of the execution mode by default as part of this function remain “Waiting to execute”, i.e. all functions called in it are executed with waiting in its thread, unless mode change is explicitly specified using methods discussed above.
***
# 7 Features of Function Execution by Robots in Different Modes
Before considering the modes of function execution by robots, we must understand how the commands are sent for robot representation in RCML environment, which in turn sends the commands to a physical robot. Sending commands to the physical robot is the responsibility of robot module, and sending commands to robot representation is the responsibility of RCML environment. Each robot representation has a queue of commands. When the interpreter calls the robot functions, this queue is filled with commands indicating the required functions to be executed. Robot representation reads the commands in sequence, sending them further to robot module. The next command will be read only after robot module reports on completion of another function. In this case, the commands may be sent to robot representation by RCML environment in two basic modes:

* Waiting to execute the function – when calling the robot function, RCML interpreter puts a command in the command queue of robot representation and waits for its response on completion or failure to complete this particular command;
* Not waiting to execute the function – in this mode, RCML interpreter also puts a command in the command queue of robot representation, but does not wait for report on completion and continued execution of the main program.

Obviously, modes of function execution by the robot coincide with modes of function execution in RCML environment, therefore, modes of function execution by the robots can be controlled by the same means as used for control of modes of function execution by RCML environment.

Example of mode flag use:
```
~robot_test->do_something(1000);
#robot_test->do_something(1000);
@r = robot_test;
~@r->do_something(1000);
#@r->do_something(1000);
```
Example of set system function use:
```
@r = robot_test;
set(“behavior”,~);
//all subsequent robot function calls will be executed
//without waiting for completion
@r->do_something(1000);
@r->do_something(1000);
@r->do_something(1000);
//mode does not change, because the flag is the same as the default value
~@r->do_something(1000);
//explicit mode change, but only for this particular function call
#@r->do_something(1000);
```
It should be noted that it is quite possible that when the command queue of robot representation is full with commands without waiting for execution, and a command with waiting for execution comes. In this case, RCML interpreter first waits for completion of all commands that came before the command with waiting for execution, then executes this command, and then continues execution of the main program.

By analogy with the call of common functions, robot function cannot be called in a mode without waiting for execution, if the result of its call is used anywhere.

Robot release operator like robot function call is the transfer of commands to the robot, but of system type – to release. Therefore, the mechanism of execution flags is also applicable to robot release operator:
```
~delete @r;
#delete @r;
```
Earlier in [Section "Releasing the Engaged Robot"](http://rcml.info/eng/aboutrcml#paragraph-171) it was noted that when the interpreter reaches end of the function, robots used in it and not released by the operator using delete, will be automatically released by the interpreter. Such robot release is identical to transfer a command for release to the robot with waiting for execution, i.e., when reaching the end of the function the interpreter waits for completion of all commands by robots engaged in this function. In order for the interpreter not to wait for completion of all commands sent to the robot, a command for release must be explicitly sent to the robot without waiting for its execution. Such action as if excludes the robot from the list of robots waiting for release. However, when reaching the end of the executable program, before completing its work, the interpreter waits for release of all robots, regardless of the mode of execution of their functions.
***
# 8 Batch Data Transmission to Robots

As noted earlier, robot representation in *RCML* environment has a command queue that is filled with commands by calling robot functions from *RCML* code. When a command comes to an empty queue, it (the command) will almost immediately be sent to the robot for execution. While the first command is being executed, all newly received commands are queued. This is because the robot generally executes the function in the material world slower than *RCML* interpreter executes the next RCML code and reaches the next robot function call, i.e., usually the robot actions are “slower” than actions of a computing processor.

However, there may be another situation where the robot must execute a series of rapid movements without stopping, and calculation of the parameters of these movements takes longer than the movements are executed. In that case, it is more efficient to pre-calculate movement parameters and immediately send the batch of commands to the robot with the calculated parameters so as the robot did not wait for the next command. There may be the cases when function call mechanism works slower than the robot executes the commands, and there is a delay in a series of rapid movements.

To compensate this effect, a mechanism of batch command sending to the robot ha been introduced. The commands received through robot function calls, can be combined into a single batch and sent it entirely to robot representation. To send a command to the batch, place “>” character before function call. To send the batch for execution, call *send_package()* system function.

Example:
```
//sending a command to the batch
>robot_test->do_something(1000);
//sending the batch for execution
system.send_package();
```
In this example, a request is first sent to a free robot of test class, and only when the robot is found, the function will be sent to the batch and then the batch will be sent. As for robot function calls, in case of batch command transfer robots-executors are reserved in advance. After calling send_package function, you can compile a new batch, including without waiting for the execution of the previous batch. The details are given later.

As is known, in the course of execution of the previous example, two commands will be sent to the batch, while there is only one function call. This is directly the command of function execution and the command to release. The batch of commands for one robot can include multiple function calls. However, it is clear that a special variable must be used for this:
```
@r = robot_tarakan;
>@r->move(1, 100);
>@r->move(1, 100);
>@r->move(1, 100);
system.send_package();
```
After executing the batch of code commands, the robot associated with *@r* variable, will not be released. It can be used further and released explicitly with delete operator, or implicitly and automatically when the interpreter reaches the end of the function. However, robot release command can also be included in the batch:
```
@r = robot_tarakan;
>@r->move(1, 100);
>@r->move(1, 100);
>@r->move(1, 100);
>delete @r;
system.send_package();
```
Now the robot will be released in the batch execution, and the robot will no longer be available in the code after send_batch function call.

A batch can be compiled for two robots at once. The commands of this batch will be sent to two robot representations. Additionally you can also combine the types of function calls:
```
>robot_test->do_something(1000);
@r = robot_test;
>@r->do_something(1000);
>delete @r;
system.send_package();
```
Another example:
```
>robot_test->do_something(1000);
>robot_test->do_something(1000);
system.send_package();
```
According to this example, two robots will be engaged at the same time (not one robot twice in succession). In this case, execution of the first commands in the queue of each robot representation will start simultaneously.

This mechanism synchronizes the time when different robots start executing their commands. In some cases it allows to synchronize the actions (within the capabilities of the robotic system used, including the operating system and other software). For example:
```
@r1 = robot_test;
@r2 = robot_test;
>@r1->do_something(1000);
>@r2->do_something(1000);
system.send_package();
>@r1->do_anything(1000);
>@r2->do_anything(1000);
system.send_package();
```
Each batch contains one function for each robot. The interpreter sends commands for simultaneous execution and waits for completion of command execution by both robot representations before compiling commands into a next batch.

In case of special robot designing, one can achieve the effect when a robot executes several actions simultaneously waiting for completion of the whole batch of commands. Such and effect may be relevant for mobile robots when moving to a new point in space, the robot performs some more action, for example, aims a weapon, fires, etc.

It is important to note, that the command batch exists only in the context of the current function, i.e. each function has its own batch:
```
function foo() {
	>robot_test->do_something(1000);
	>robot_test->do_something(1000);
}
function main() {
	foo();
	system.send_package();
}
```
In the course of execution of this command example, functions “foo” placed in the batch will never be executed, because the batch will be destroyed with completion of this function. And an empty batch will be sent to “main” function. In this case, two robots will be engaged and be inactive until the end of the program, as the commands to release them were placed in a batch that has not been executed. This is another example of robot leakage in RCML.

It was noted earlier that send_package function takes one parameter – execution mode flag, which tells the interpreter how to execute the batch:

* #- the interpreter will wait for execution of the whole batch of commands:
```
system.send_package(#);
```
* ~ - the interpreter will not wait for execution of the batch of commands and immediately after sending the batch will continue execution of the current function code:
```
system.send_package(~);
```
Additionally, send_package function is the only system function that takes into account the execution flag before its call, including the default execution flag:
```
// placing a command to the batch
>robot_test->do_something(1000);
// sending the batch without waiting for completion
~system.send_package();
// generating a new batch, although the previous one is still executing
>robot_test->do_something(1000);
// changing the default execution flag
system.set(“behavior”, ~);
// sending the batch without waiting again, already having to executing batches
system.send_package();
// generating the next batch
>robot_test->do_something(1000);
// and now sending a batch with waiting for its execution
system.send_package(#);
```
As can be seen from the above example, to have the highest priority among the ways to specify function execution mode in the batch is to specify the mode flag as an argument to send_package function, followed by an explicit specification of the mode flag before the function call; and execution mode flag set by default as part of this function has the lowest priority.
***

# 9 Communication with the OS

### 9.1 Passing parameters to the RCML program

The data from the OS may be passed to the RCML program via the command line. However, at this stage of language development only real data types may be passed. In order to have access to the parameters in the RCML code passed via the command line, add necessary parameters to the *main* function.

The values are passed to these parameters of the *main* function by adding the parameter value to the invocation command of the RCML interpreter after specifying the file with the PC code. Passing a parameter with a value will be as follows:
```
-Pparameter_name=parameter value
```
For example, let there be a *test.rcml* file with the following content:
```
function main(foo, bar) {
	sum = foo + bar;
	system.echo(“foo + bar = ”, sum, “\n”);
}
```
The command for compiling the RCML file:
```
rcml_compiler test.rcml test.pc
```
The command for executing the RCML program with passing value 1 to parameter *foo*, and bar equal to 3.5:
```
rcml_interpreter test –Pfoo=1 –Pbar=3.5
```
The result of program execution will be:
```
foo + bar = 4.5
```
Here, the order of passing the parameters values is not important, since they are passed by their names. The parameters whose values have not been passed will be equal to 0.0. 

In the next example:
```
rcml_interpreter test –Pfoo=1
```
The result will be:
```
foo + bar = 1
```
If the value has been passed for a non-existing parameter, an error will be thrown at the attempt to run the RCML program.

The following example contains an error, since the *err* parameter does not exist:
```
rcml_interpreter test –Pfoo=1 –Perr=2.35
```
### 9.2 passing values from the RCML program to the OS

Data may be passed from RCML to the OS in two ways:

* passing data via the standard output using the *system.echo* function (read more in [Section "System Functions in RCML"](http://rcml.info/eng/aboutrcml#paragraph-165));
* passing the value as a program termination code (read more in [Section "Other Control Structures"](http://rcml.info/eng/aboutrcml#paragraph-118)):
  * via the *return* operator;
  * via the *exit* operator.
 
### 9.3 Passing exceptions (errors) from the program to the OS

It occurs by throwing an exception without catching it up to the top level, i.e., before and including the *main* function. In this case the RCML program will terminate with code 1, and the value of the exception will not be passed. The mechanism of processing and passing exceptions is discussed in more details in Section ["Exceptions"](http://rcml.info/eng/aboutrcml#paragraph-163).
***

# 10 Creating RCML Library File

The following nomenclature of description of file to be created shall be used to create a library.

The very first line shall include a library interface identifier (IID) – a unique set of 32 bytes describing the library interface version, i.e., the current set of functions provided by a library, and their parameters. This IID must be unique, because the interpreter distinguishes between different libraries using this parameter only. To make it more unique, it is recommended to use all 32 bytes of the length permitted. Other bytes following the 32th byte, will be discarded.

Syntax:
```
IID “interface_identifier”
```
Example library ID names:
```
IID “test_lib_v1.0"
```
When writing library functions, a programmer can specify which ones to export, i.e. to make available to call from other programs, as in Section ["Calling Robot Function"](http://rcml.info/eng/aboutrcml#paragraph-169). You can label the function for export by placing export keyword before function keyword:
```
export function function_name(parameter_list) {
	operator_list
}
```
Robot functions are exported similarly:
```
export function robot_class::function_name(parameter_list) {
		operator_list
}
```
Example library listing:
```
UID "test_lib_v1.0"
export function sum(a,b){
	c = a + b;
	return c;
}
export function robot_test::user_function(s) {
	robot->print("Robot delay\n", 1000);
	robot->print("Robot Func All right\n", 0);
}
```
It should be remembered that inclusion of the library into another program makes this program dependent on all modules and other libraries used in that library. I.e., to execute such a program, the interpreter requires modules and libraries with the same IID, which were used in its (program) compilation. Dependence on IID is not dependence on a particular module or library file. Library or module can be changed, improved or worsened, but if a set of functions and their parameters exported to RCML environment (i.e. IID) remained unchanged, such library is considered to be equal to the original one. Read more about IID mechanism in Section ["Additional Information About Interface Identifiers"](http://rcml.info/eng/aboutrcml#paragraph-63).
***

# 11 The List Of Reserved Keywords in RCML

||||||| 
|:--|:--|:--|:--|:--|:--|
|break|catch|continue|define|delete|else|
|exit|export|function|if|IID|include|
|include_lib|loop|return|robot|throw|try|

***

# 12 Syntax Map of RCML

```
PROGRAM: IID HEADERS

IID:
|       IID_KEYWORD STRING

HEADERS:
|           HEADERS INCLUDE
|           HEADERS DEFINE 

INCLUDE:    include STRING
|           include_lib ID STRING

PROGRAM: FUNCTIONS

FUNCTIONS:
|           FUNCTIONS FUNCTION

FUNCTION:   EXPORT_FLAG function ROBOTS :: ID '(' ID_LIST ')' BLOCK_CODE
|           EXPORT_FLAG function ID '(' ID_LIST ')' BLOCK_CODE

EXPORT_FLAG:
|               export

ROBOTS: ROBOT_ID
|       ROBOTS ',' ROBOT_ID

ID_LIST:
|       ID
|       ID_LIST ',' ID

BLOCK_CODE: '{' OPERATORS '}'

OPERATORS: OPERATOR
|          OPERATORS OPERATOR

OPERATOR: OPER ';'
|         if '(' EXPR ')' BLOCK_CODE
|         if '(' EXPR ')' BLOCK_CODE else BLOCK_CODE
|         loop BLOCK_CODE
|         try BLOCK_CODE
|         try ARGS_BLOCK BLOCK_CODE
|         try BLOCK_CODE catch BLOCK_CODE
|         try ARGS_BLOCK BLOCK_CODE catch BLOCK_CODE
|         try BLOCK_CODE catch '(' ID ')' BLOCK_CODE
|         try ARGS_BLOCK BLOCK_CODE catch '(' ID ')' BLOCK_CODE

OPER:     FLAG_WAIT ROBOT_LINK -> ID ARGS_BLOCK
|         FLAG_WAIT ROBOT_LINK -> '.' ID ARGS_BLOCK
|         FLAG_WAIT ID ARGS_BLOCK
|         FLAG_WAIT ID '.' ID ARGS_BLOCK
|         ROBOT_LINK -> ID ARGS_BLOCK
|         ROBOT_LINK -> '.' ID ARGS_BLOCK
|         ID ARGS_BLOCK
|         ID '.' ID ARGS_BLOCK
|         EXPR
|         break
|         continue
|         exit
|         exit EXPR
|         return
|         return EXPR
|         delete ROBOT_VAR
|         FLAG_WAIT delete ROBOT_VAR
|         throw
|         throw EXPR
|         ROBOT_ASSIGN

ROBOT_ASSIGN: ROBOT_VAR '=' ROBOT_ASSIGN
|             ROBOT_VAR '=' ROBOT_LINK

EXPR:   EXPR1
|       ID '=' EXPR

EXPR1:  EXPR2
|       EXPR1 == EXPR2
|       EXPR1 <= EXPR2
|       EXPR1 >= EXPR2
|       EXPR1 != EXPR2
|       EXPR1 '>' EXPR2
|       EXPR1 '<' EXPR2
|       EXPR1 && EXPR2
|       EXPR1 || EXPR2

EXPR2:  TERM
|       EXPR2 '+' TERM
|       EXPR2 '-' TERM

TERM:   VAL
|       TERM '*' VAL
|       TERM '/' VAL
|       TERM '%' VAL

VAL:    NUM
|       '-' VAL
|       '!' VAL
|       '(' EXPR1 ')'
|       ID
|       ID ARGS_BLOCK
|       ID '.' ID ARGS_BLOCK
|       ROBOT_LINK -> ID ARGS_BLOCK
|       ROBOT_LINK -> '.' ID ARGS_BLOCK

ROBOT_LINK: ROBOT_VAR
|           ROBOT_ID CHOICE_MAKER
|           robot CHOICE_MAKER

CHOICE_MAKER:
|             '<' ID_LIST '>'

FLAG_WAIT:  #
|           ~
|           '>'

ARGS_BLOCK: '(' ARGS ')'

ARGS:
|       ARG
|       ARGS ',' ARG

ARG:    EXPR
|       STRING
|       FLAG_WAIT
|       ROBOT_LINK
```
***
# 13 Creating Own Modules for RCML

All description of classes in *C++* programming language cited in this section, along with the appropriate header files, as well as examples of modules can be found in the relevant official repository at GitHub service:

https://github.com/RobotControlTechnologies/module_headers

All types of modules in *RCML* environment are created by dynamic link libraries (dll files for the OS of Windows family, or so files for the OS of Linux family). A variety of APIs to interact with the robot are implemented by using them. They are described below.

Unification of the interaction interface at the software level provides the developer with advantages in choosing the hardware interfaces, hardware itself, communication methods, technologies, etc. in solving tasks of integration of its solutions with RCML environment.

The instructions below will be given with respect to C++ programming language, but you can use any other programming language to create modules, it is important that dynamic link libraries provided certain interfaces, similar to those used to implement in C++.

Regardless of the type of module, module library must export only two functions:

* A function that returns API version supported by the module;
* A function that returns a pointer to the descendant of the abstract class of the corresponding module type.
However, the names of these functions, as well as the result returned depend on the module type. These functions do not have parameters, and their names must be strictly defined.

These functions are called only once at the launch of the compiler or interpreter. Accordingly, it is guaranteed that the object with a pointer returned by this function, is always in the singular as part of a single instance of *RCML* environment.

### 13.1 Robot Module

As noted earlier, to be able to send commands to a real physical robot through *RCML*, this robot must be represented in *RCML* environment by robot module.

Robot module library must export the following functions:
```
unsigned short getRobotModuleApiVersion();
RobotModule* getRobotModuleObject();
```
*getRobotModuleApiVersion* function returns the version of *API* of robot module in a given module as a positive integer. The best way to determine this function is to use the following code:
```
unsigned short getRobotModuleApiVersion() { return ROBOT_MODULE_API_VERSION; }
```
*ROBOT_MODULE_API_VERSION* constant is defined in *robot_module.h* linked file, which is required when compiling robot module. Thus, we can omit specifying a version manually, and it will match the module interface used.

*getRobotModuleObject* function returns a pointer to the object of the abstract class RobotModule, essentially describing the required robot module interface.

Hereinafter we will refer *RobotModule* class as the abstract robot module. Correspondingly, the class pointed by the returned pointer, must be inherited from it (the abstract robot module) and must re-define all virtual methods of this class, maintaining mode of application to them. This subsidiary class will be referred to as robot module.

RobotModule class description in *C++*:
```
class RobotModule {
 protected:
  RobotModule() {}

 public:
  // init
  virtual const struct ModuleInfo& getModuleInfo() = 0;
  virtual void prepare(colorPrintfModule_t *colorPrintf_p, colorPrintfModuleVA_t *colorPrintfVA_p) = 0;

  // compiler only
  virtual FunctionData **getFunctions(unsigned int *count_functions) = 0;
  virtual AxisData **getAxis(unsigned int *count_axis) = 0;
  virtual void *writePC(unsigned int *buffer_length) = 0;

  // intepreter - devices
  virtual int init() = 0;
  virtual void final() = 0;

  // intepreter - program & lib
  virtual int readPC(int pc_index, void *buffer, unsigned int buffer_length) = 0;

  // intepreter - program
  virtual int startProgram(int run_index, int pc_index) = 0;
  virtual AviableRobotsResult *getAviableRobots() = 0;
  virtual Robot *robotRequire(Robot *robot) = 0;
  virtual void robotFree(Robot *robot) = 0;
  virtual int endProgram(int run_index) = 0;

  // destructor
  virtual void destroy() = 0;
  virtual ~RobotModule() {}
};
```
*getModuleInfo* method must return the structure that describes this module. Description of this structure is given below. This method can be called multiple times in one work session.

*prepare* method is called once immediately after the module is loaded into memory. Two pointers to system functions of *RCML* environment allowing the logged output of various data are sent as parameters of the method. Output from these procedures is sent to standard output and can be formatted and written to the relevant log files of *RCML* environment.

Functions, to which pointers are sent, have the following description:
```
void colorPrintfFromModuleVA(void *module, ConsoleColor colors, const char *mask, va_list args);
void colorPrintfFromModule(void *module, ConsoleColor colors, const char *mask,...);
```
A pointer to the current instance of robot module, i.e. this must be sent as *module* parameter. *ConsoleColor* structure must be sent as colors parameter. This parameter sets the output color at the console. The third parameter (mask) defines formatting mask similar to printf function from the standard library of C++. Further, depending on output function, according to the defined formatting mask, goes common enumeration of parameters or enumeration of parameters in the form of va_list structure from *std_arg.h*.

*getFunctions* method is called only by the compiler. It must return a pointer to an array of pointers to *FunctionData* structures, describing every available robot function. And this method takes one parameter by link – *count_functions*, which must include the number of array elements with pointers to FunctionData, see details below. If robot module does not provide any functions, this method must return *NULL* pointer, and write 0 to *count_functions* parameter.

*getAxis* method is also called by the compiler only. It must return a pointer to an array of pointers to *AxisData* structures, describing each available axis of robot control for hand control mode. This method, similar to the previous one, takes one parameter by link – *count_axis*, which must include the number of array elements with pointers to *AxisData*, see details below. If robot module does not support hand control mode, this method must return *NULL* pointer, and write 0 to count_axis parameter.

*writePC* method is called only by the compiler. This method allows the module to write (to the file of the program compiled) arbitrary data with the pointer to their range to be returned by the method as a result, and the length of data recorded in *buffer_length* parameter transferred by link.

*init* method is used for robot module to execute procedures necessary to get properly started, for example, reading the configuration file, establishing communication with robots, etc. The method is called once by the interpreter only at launch, immediately after loading dll library in memory and receiving a pointer to an instance of robot module. This method must return 0, if there were no errors when loading, and calling environment can continue operation. Otherwise – any other value different from 0, and calling program accordingly ends operation generating an error on impossibility to initialize this module.

*final* method is used for robot module to execute procedures necessary for correct completion of work, for example, saving some intermediate values, switching off of the robots, etc. This method, like init method, is called only once by the interpreter upon completion of the main program execution.

The *readPC* method is only called by the interpreter when loading another program or library into memory; if it contains data recorded by this module, a pointer to the domain of these data will be passed to the buffer parameter, and the size of this data will be passed to the *buffer_length* parameter. The *pc_index* parameter is a unique index of the downloaded *PCode* file (this index is used in other methods, see later). If there is no data recorded by the module, this method will not be called.

The *startProgram* method is only called by the interpreter before executing the next *RCML* program, the *run_index* parameter will be a unique number – program run ID, and the *pc_index* parameter will be a unique index of the loaded *PCode* file, from which the program is executed. The file can be loaded once, but the program can be invoked several times; it can even be invoked in parallel threads. If this method returns 0, the program will be passed for invocation. Otherwise, getting any other value different from 0, the *RCML* environment will not execute the program, and will show an error stating that the module prohibits running the current program. This method allows restricting program invocation, based on the data obtained in the *readPC* method.

The *getAviableRobots* method returns a pointer to the *AviableRobotsResult* structure, which contains information about all the robots that are available in this module at the time of request.

The *robotRequire* method is only called by the interpreter at the point of program execution, when the necessity occurs to use a robot of some class that is associated with the robot module. The robot parameter contains a pointer previously obtained from the *getAviableRobots* method, to an instance of the class inherited from abstract class Robot mapped to the particular requested physical robot. The robot module should check that the requested robot is available, and if it is, activate the requested robot, and return a pointer to it, otherwise return *NULL*, which means that this robot cannot be activated. If the robot parameter is equal to *NULL*, the robot module may return any available robot or, if there are no available robots. 

**Important**! It should be noted that this method should not delay execution to wait for an available robot; this will be done by internal mechanisms of the *RCML* environment.

*robotFree* method is called only by the interpreter when a certain engaged robot has ceased to be necessary for a specific program and can be released. A pointer that was previously obtained from robotRequire method will be sent as a parameter. It is guaranteed that this pointer is not changed during program execution.

The *endProgram* method is also called only by the interpreter after execution of the *RCML* program, the same run ID of the program will be passed as the *run_index* parameter, as the one passed for calling the *startProgram* method, which describes the program. If this method returns 0, it is considered that the program has been executed without errors, and its completion code will not be changed. If any other number is returned, the program is considered to have worked with errors, and the completion code will be different from 0. This method allows the module to report at the end of the program that the execution of this program with this module was not correct, and there were errors during execution.

*destroy* method is the alias of robot module destructor. It must call a destructor of robot module object, if necessary, and release engaged resources, since *RCML* environment does not call the destructor method due to the nature of the code generated by different compilers. This method is called once by the compiler and interpreter upon completion of work before unloading of the module from memory by a certain instance of the interpreter or compiler (by calling *FreeLibrary* functions).

Remember that multiple instances of the interpreters and/or compilers solving different tasks can work simultaneously, so each instance will call getRobotModuleObject function from the module file, and, accordingly, *init, final, destroy* and other methods in the course of operation. Moreover, given the nuances of multithreading, it should be noted that they can be called simultaneously in time.
```
Therefore, robot module must be thread-safe in all respects!
```
In implementing robot module, it is recommended to use singletone design pattern, given that methods of robot module can be called from different threads and even simultaneously.

The previously mentioned *ModuleInfo* structure, describing the module is as follows:
```
struct ModuleInfo {
  char *iid;
enum Modes { PROD, SPEC } mode;
  unsigned short version;
  char *digest;
};
```
The following items are given in this structure:

*iid*– identifier of the interface of this module. This line should not be longer than 32 bytes Read more in Section ["Additional Information About Interface Identifiers"](http://rcml.info/eng/aboutrcml#paragraph-63).

*mode* – type of version of this module file. It can be *PROD* – production version, or *SPEC* – specification version. Production version involves fully-featured module file, declaring module functions and contains the code for their execution. Specification version assumes that the module file only declares module functions in *RCML* environment, but does not include a code for their execution. Accordingly, production version of the module can also be used to compile *RCML* programs with this module and for their execution, while specification version can only be used for compilation. The interpreter will not work with specification version of the module. This mechanism allows developers of robot modules to declare robot module interface without transferring its master code. This can be useful when working with contractors that write software for this module. They may have means of verifying the proper use of the interface, but will not be able to get the real module.

*version* – the current module version as a positive integer. If the module is not planned to be distributed through the Repository service, this field does not play a great role. But otherwise, you need to consider the fact that the Repository requires that the next version of the loadable module was higher than previously loaded one.

*digest* – a pointer to the digital signature of the module developer. It is used only by the Repository service.

*FunctionData* structure is used to describe each function of the robot available for use in *RCML* language. Description of the structure in C++:
```
structFunctionData {
enumParamTypes { STRING, FLOAT };

system_value command_index;
  unsigned int count_params;
  ParamTypes *params;
  const char *name;
  FunctionData() : command_index(0), count_params(0), params(NULL), name(NULL) {}
  FunctionData(system_value command_index, system_value count_params,ParamTypes *params, const char *name) : command_index(command_index),count_params(count_params),params(params),name(name) {}
};
```
The following items are given in this structure:

*command_index* – a unique command identifier within the module. This value will further be used and sent to robot representation by the interpreter to indicate the command to be executed by a particular robot. Command index must be an integer greater than zero. Values of zero and negative values are reserved and are system values;

*count_params* – a number of function parameters equal to the number of elements in params array;

*params* – a pointer to the array of parameter types. Each array element must be represented by ParamTypes counting element: *STRING* – a line or *FLOAT* – a number. See detailed description of the mechanism of parameter transfer below;

*name* – a pointer to the name of a function specified by *RCML* programmer to call this function.

In turn, *AxisData* structure is used to describe each robot control axis available for use in hand_control system function in *RCML*. Description of this structure in *C++*:
```
struct AxisData {
  system_value axis_index;
  variable_value upper_value;
  variable_value lower_value;
  const char *name;
  AxisData() : axis_index(0), upper_value(0), lower_value(0), name(NULL) {}
  AxisData(system_value axis_index, variable_value upper_value,variable_value lower_value, const char *name) : axis_index(axis_index),upper_value(upper_value),lower_value(lower_value),name(name) {}
};
```
The following items are given in this structure:

*axis_index* – a unique axis identifier within the module. This value will further be used and sent to robot representation by the interpreter to indicate the axis value to be changed. Similar to unique command identifiers, axis index must be an integer greater than zero;

*upper_value* – the upper limit value, which can be transferred for a given axis;

*lower_value* – the lower limit value, which can be transferred for a given axis;

*name* – a pointer to the axis name specified by *RCML* programmer to call hand_control system function to link to this axis.

The description of previously mentioned structure *AviableRobotsResult* that describes the list of available modules:
```
struct AviableRobotsResult {
  Robot **robots;
  unsigned int count_robots;
  AviableRobotsResult(Robot **robots, unsigned int count_robots) : robots(robots), count_robots(count_robots) {}
  void destroy() { delete this; }
  ~AviableRobotsResult() { delete[] robots; }
};
```
*robots*  is an array of pointers to available robots;;

*count_robots*  is the number of elements in the robots array.

*Further*, robot representation means a class to which instance a pointer is returned by robotRequire method. And this class must be inherited from the abstract Robot class, hereinafter referred to as an abstract robot representation. Essentially, an instance of robot representation is a representation of a physical robot in *RCML* environment, which was mentioned in description of the language syntax and which *RCML* environment interacts with a particular real robot through. Robot representation must implement all public methods of abstract robot representation. Description of the abstract Robot class in C++:
```
class Robot {
 protected:
Robot() {}

 public:
  virtual void prepare(colorPrintfRobot_t *colorPrintf_p, colorPrintfRobotVA_t *colorPrintfVA_p) = 0;
  virtual FunctionResult *executeFunction(CommandMode mode, system_value command_index, void **args) = 0;
  virtual void axisControl(system_value axis_index, variable_value value) = 0;
  virtual ~Robot() {}
};
```
*prepare* method is called only by the interpreter immediately after receiving a pointer to an instance of robot representation (through robotRequire module method), by analogy with the same method in the module, this method provides access to logging means in *RCML* environment. Pointers to functions with the following descriptions are sent to this method as parameters:
```
void colorPrintfFromRobotVA(void *robot, const char *uniq_name, ConsoleColor colors, const char *mask, va_list args);
void colorPrintfFromRobot(void *robot, const char *uniq_name, ConsoleColor colors, const char *mask, ...);
```
A pointer to the current instance of robot representation, i.e., this must be sent as robot parameter. *uniq_name* parameter can sent a pointer to a line – robot name, or it can be *NULL* value, then robot name will be replaced by its serial number at the output. *ConsoleColor* structure must be sent as colors parameter. The third parameter (*mask*) defines formatting mask similar to printf function from the standard library of *C++*. Further, depending on output function, according to the defined formatting mask, goes common enumeration of parameters or enumeration of parameters in the form of va_list structure from std_arg.h.

*executeFunction* method is called by the interpreter when a certain physical robot matched to this robot representation, must execute the desired function. In this case, one of the values of CommandMode enumeration will be sent to mode parameter. Through this parameter, robot representation can learn how the current function is exactly called. However, the actions of robot representation have no impact on waiting or not waiting of execution of the called function by *RCML* environment. The value of this parameter can be as follows:

* wait – the command is executed waiting for the execution;
* not_wait – the command is executed not waiting for the execution;
* package – the command is sent in the package, and is not the last command in the package;
* end_of_package_wait – the last command in the package; the package is executed waiting for the execution;
* end_of_package_no_wait – the last command in the package; the package is executed not waiting for the execution.

A unique function identifier will be sent to *command_index* parameter. A pointer to an array of parameters of called functions will be sent to args parameter. The number of elements in this array will be equal to the number of parameters takes by this function, i.e., the number of parameters specified in count_params property of the instance of *FunctionData* structure corresponding to this function, which, as mentioned earlier, *RCML* environment will receive by calling *getFunctions* method. The sequence of parameter values in the array is direct, just as they were specified in a text file of a program in *RCML*, and just as the types of these parameters in params property were specified. A pointer to the parameter value which must receive text data, will point to a line (char *), and, respectively, a pointer to the parameter value receiving numerical data – to the number of variable_value type (double *). This method must return the function result, which is a pointer to FunctionResult structure. If the called function takes no parameters, *NULL* will be sent to args parameter of this method.

This method can be called specifying one of the following system values (named constants) as the value of command_index parameter, while NULL value will be sent to args parameter:

*ROBOT_COMMAND_FREE* – is sent when this particular robot will be no more needed to the program. It should be noted that this parameter applies only to notify a certain robot that it will be further released, but it is not a command to release the robot for robot module. To release the robot, robotFree method of robot module is called;

*ROBOT_COMMAND_HAND_CONTROL_BEGIN* – preparing a robot to switch to hand control mode; after processing this command, the commands will no more be sent to the robot, and axis values will be sent through axisControl method;

*ROBOT_COMMAND_HAND_CONTROL_END* – exiting hand control mode.

*axisControl* method is called by the interpreter only in hand control mode. A unique identifier of robot axis from AxisData structure is sent as the first parameter of this method. A new value for this axis is sent as the second parameter. A mechanism of transfer is described in Section ["Value Transfer Principle"](http://rcml.info/eng/aboutrcml#paragraph-174).

Description of FunctionResult in C++:
```
class FunctionResult {
 public:
  enum Types { EXCEPTION, VALUE };
 private:
  Types type;
  variable_value result;

public:
  FunctionResult(Types type) : type(type), result(0.0f) {}
  FunctionResult(Types type, variable_value result) : type(type), result(result) {}
  virtual Types getType() { return type; }
  virtual variable_value getResult() { return result; }
  virtual void destroy() { delete this; }
  virtual ~FunctionResult(){};
};
```
The following items are given in this structure:

*type* – the type of value returned by robot function. If this type is *EXCEPTION*, it is considered that the function threw an exception that can be handled by try operator, otherwise it is considered that the function returned the value;

*result* – the value returned by robot function; it will be read only if type is not *EXCEPTION*. This value may be just a number.

**Important**! During the compiler execution, working methods of the *getModuleInfo* robot module, prepare, *getFunctions, getAxis, writePC* and *destroy* are called from the same thread. During the interpreter execution, the methods of the robot module *prepare, init, final, readPC, startProgram, endProgram, destroy* are called sequentially from the same thread. Methods *getAviableRobots, robotRequire* and *robotFree* are called within a single thread, but it's not the same thread that the one in the previous case. The *getModuleInfo* method is called from various threads at arbitrary points in time.

### 13.2 Function Module

Function modules are used to extend the set of system functions of RCML environment (do not confuse with functions of system module). Because module functions can be created in traditional programming languages and tools, it is possible to integrate RCML environment with other software and technologies, and hence integrate it with robotics through RCML environment.

By its structure and principles of interaction, function modules are very similar to robot modules, so it is recommended to read the previous section.

Dynamic link library of function module must export the following functions:
```
unsigned short getFunctionModuleApiVersion();
FunctionModule *getFunctionModuleObject();
```
Hereinafter we will refer FunctionModule class as the abstract function module. Correspondingly, the class pointed by the returned pointer, must be inherited from it and must re-define all virtual methods of this FunctionModule class, maintaining mode of application to them. This subsidiary class will be referred to as function module.

Description of FunctionModule class in C++:
```
class FunctionModule {
 protected:
  FunctionModule() {}

 public:
  // init
  virtual const struct ModuleInfo& getModuleInfo() = 0;
  virtual void prepare(colorPrintfModule_t *colorPrintf_p, colorPrintfModuleVA_t *colorPrintfVA_p) = 0;

  // compiler only
  virtual FunctionData **getFunctions(unsigned int *count_functions) = 0;
  virtual void *writePC(unsigned int *buffer_length) = 0;

  // intepreter - devices
  virtual int init() = 0;
  virtual void final() = 0;
  
  // intepreter - program & lib
  virtual int readPC(int pc_index, void *buffer, unsigned int buffer_length) = 0;

  // intepreter - program
  virtual int startProgram(int run_index, int pc_index) = 0;
  virtual FunctionResult *executeFunction(system_value function_index, void **args) = 0;
  virtual int endProgram(int run_index) = 0;

  // destructor
  virtual void destroy() = 0;
  virtual ~FunctionModule() {}
};
```
The *getModuleInfo, prepare, writePC, readPC, init, final, startProgram, endProgram* and *destroy* methods are completely similar to the homonymous methods of the robot module.

*getFunctions* method is similar to the same method of robot module; when it is called, function module must return a list of functions that it provides for calling from RCML environment.

*executeFunction* method is similar to the same method of robot representation, but the method of this type of modules has no *mode* parameter and when it (method) is called, the robot does not execute the function, but control is simply passed to the appropriate function of function module.

*FunctionData* and *FunctionResult* structures were described in the previous section.

### 13.3 Control Module

Control modules are used to connect control devices to *RCML* environment and can only be used in the system function of switching to hand control mode. By its structure, control modules are very similar to robot modules, so it is recommended to first read Section ["Robot Module"](http://rcml.info/eng/aboutrcml#paragraph-181).

Control module library must export the following function:
```
unsigned short getControlModuleApiVersion();
ControlModule *getControlModuleObject();
```
Hereinafter we will refer *ControlModule* class describing the interface of control module as the abstract control module. Correspondingly, the class pointed by the returned pointer, must be inherited from it and must re-define all virtual methods of this *ControlModule* class, maintaining mode of application to them. This subsidiary class will be referred to as control module.

Description of *ControlModule class* in *C++*:
```
class ControlModule {
 protected:
  ControlModule() {}

 public:
  // init
  virtual const struct ModuleInfo& getModuleInfo() = 0;
  virtual void prepare(colorPrintfModule_t *colorPrintf_p, colorPrintfModuleVA_t *colorPrintfVA_p) = 0;

  // compiler only
  virtual AxisData **getAxis(unsigned int *count_axis) = 0;
  virtual void *writePC(unsigned int *buffer_length) = 0;

  // intepreter - devices
  virtual int init() = 0;
  virtual void execute(sendAxisState_t sendAxisState) = 0;
  virtual void final() = 0;

  // intepreter - program & lib
  virtual int readPC(int pc_index, void *buffer, unsigned int buffer_length) = 0;

  // intepreter - program
  virtual int startProgram(int run_index, int pc_index) = 0;
  virtual int endProgram(int run_index) = 0;

  // destructor
  virtual void destroy() = 0;
  virtual ~ControlModule() {}
};
```
The *getModuleInfo, prepare, writePC, readPC, init, final, startProgram, endProgram* and *destroy* methods are completely similar to the homonymous methods of the robot module.

*getAxis* method is similar to the same method in robot module, but when calling, control module must return a list of control axes that it provides for linking in the call of *hand_control* system function.

*execute* method is called by the interpreter when switching to hand control mode. A pointer to the function of sendAxisState_t type will be sent as the only parameter of this method, defined as follows:
```
typedef void (*sendAxisState_t)(ControlModule *, system_value, variable_value);
```
This function must be called each time when the control device changes a value of the control axis. A pointer to this control module is sent as the first parameter of this function, followed by a unique axis identifier that was specified in the data returned by *getAxis* method. A new value of this axis is sent as the last parameter.

*AxisData* structure was described in Section ["Robot Module"](http://rcml.info/eng/aboutrcml#paragraph-181).

#### 13.4 Choosing module

The choosing modules provide algorithms for decision-making. Like other modules, the choosing modules have methods that are common to all modules, as first described in the "Robot module" section.

The library of the choosing module should export the following functions:
```
unsigned short getChoiceModuleApiVersion();
ControlModule *getChoiceModuleObject();
```
Further, the abstract control module will be understood as the *ChoiceModule* class, which describes the interface of a choosing module, and, accordingly, the class to which the returned pointer is pointing should inherit from it, and override all virtual methods of this class, preserving the mode of calling then; this child class will be further referred to as the statistics module.

*ChoiceModule* class description in C++:
```
 class ChoiceModule {
 protected:
  ChoiceModule() {}

 public:
  // init
  virtual const struct ModuleInfo& getModuleInfo() = 0;
  virtual void prepare(colorPrintfModule_t *colorPrintf_p, colorPrintfModuleVA_t *colorPrintfVA_p) = 0;

  // compiler only
  virtual void *writePC(unsigned int *buffer_length) = 0;

  // intepreter - devices
  virtual int init() = 0;
  virtual void final() = 0;

  // intepreter - program & lib
  virtual int readPC(int pc_index, void *buffer, unsigned int buffer_length) = 0;

  // intepreter - program
  virtual int startProgram(int run_index, int pc_index) = 0;
  virtual const ChoiceRobotData *makeChoice(const ChoiceFunctionData** function_data, unsigned int count_functions, const ChoiceRobotData** robots_data, unsigned int count_robots) = 0;
  virtual int endProgram(int run_index) = 0;

  // destructor
  virtual void destroy() = 0;
  virtual ~ChoiceModule() {}
};
```
The *getModuleInfo, prepare, writePC, readPC, init, final, startProgram, endProgram* and *destroy* methods are completely similar to the homonymous methods of the robot module.

The *makeChoice* method is called when the choosing module is required to make a decision about choosing a robot. It is in this method that the robot choosing logic has been implemented. The following parameters are passed to the input of this method:

* *function_data* - the array of pointers to the *ChoiceFunctionData* structures that represents the sequence of functions of the robot to be chosen.
* *count_functions* - the number of elements in the *function_data array*;
* *robots_data* - the array of pointers to the *ChoiceRobotData* structures; this array has a list of pointers to descriptors of available robots obtained from robot modules;
* *count_robots* - the number of elements in the *robots_data* array.

The *makeChoice* method should return a pointer to the description of the *ChoiceRobotData* presentation of the chosen robot. If the decision about choosing the robot cannot be made, the *makeChoice* method should return *NULL*.

**Important**! If the choosing module is supposed to work with the *RCML* statistics database, section ["Working with RCML statistics"](http://rcml.info/eng/aboutrcml#paragraph-67) has an important addition about it.
```
struct ChoiceFunctionData {
  enum CallType {
    Unknow = 0,
    Guarante = 1,
    Probably = 2
  };
  const char *name;
  const char *context_hash;
  system_value position;
  CallType call_type;
}; 
```
In this structure, the following properties have been marked:
* *name* - the name of the function that will be called for the chosen robot;
* *context_hash* - the hash of the *RCML* file of the program represented as a string of binary data, where a call to this function has been detected;
* *position* - a unique position of calling this function in the file of the *RCML* program. This property should not be confused with the number of the line where this function is called in the program source code;
* *call_type* - the type of function call that may take the following values:
  * *Unknow* - the *RCML* interpreter can't compute call of this function, it is an exceptional situation equivalent to internal error;
  * *Guarante* - the function is guaranteed to be called, i.e. it does not depend on any conditions;
  * *Probably* - the function will probably be called, i.e. in the path to calling this function, there are branching conditions that may result in a failure to call this function. It should be noted that in this context, the condition is not the *if* operator, but the conditional transfer - an equivalent to the *jz* assembler command with a computed condition. Unconditional transfers do not fall into this definition.

The *ChoiceRobotData* structure that describes the representation of the robot has the following С++description:
```
struct ChoiceRobotData {
  const ChoiceModuleData *module_data;
  const char *robot_uid;
};
```
In this structure, the following properties have been marked:

* *module_data* - a pointer to the *ChoiceModuleData* structure that describes the robot module;
* *robot_uid* - the name of the robot that must be unique within the robot module, however, it may not necessarily be so. This field may also be an empty string "".

The structure of *ChoiceModuleData* is:
```
struct ChoiceModuleData {
  const char *iid;
  const char *hash;
  unsigned short version;
};
```
It contains the following elements:

* *iid* - the interface module *ID* as a string;
* *hash* - the hash of the file module in the form of a string representation of binary data;
* *version* - the version of the module.
***

# 14 Additional Information About Interface Identifiers

A line, a pointer to which is stored in *iid* property of *ModuleInfo* structure returned by *getModuleInfo* method, regardless of the module, is a unique module interface version identifier, i.e. it can be used to give uniqueness to the module and its particular version. It is similar for *RCML* libraries.

It is quite possible for different developers to create modules with the same name. Additionally, the user can rename the modules used in the file system and in configuration file of *RCML* environment, and then access them in *RCML* environment using new names. Therefore, this unique identifier was introduced in order to use the modules of *RCML* environment which the program depends on in execution, and which were used by a programmer at compilation stage. It is used to compare the required modules to execute the program with the existing modules available. However, a unique identifier without prejudice might still be the same for different types of modules.

To make the identifier more unique, it is recommended to specify a unique identifier of the maximum size permitted – 32 bytes.

The unique identifier may to some extent serve as a tool for module or library developer, pointing *RCML* environment on compatibility of different versions of the product developed. By changing the code without changing this identifier, the developer indicates succession of the new version of the module or library in relation to the old one, i.e. the new version can be used to execute *RCML* programs compiled with the old version. It is recommended to do this only if the unique indexes of functions of the module or library function names are not changed, as well as their position in the code, the number of their parameters and the program behavior executing these functions. Otherwise, it is recommended to specify a new unique interface identifier, to point *RCML* environment that a new version of the module or library has significant differences from the old one and behavior of the executed *RCML* program compiled with the old version is not defined for the new version.
***
# 15 Working with RCML statistics

### 15.1 Overview and recommendations
*RCML* writes statistics to an external database file specified in the *config.ini* configuration file (read more in Section ["Installation and Configuration of RCML Compiler and Interpreter"](http://rcml.info/eng/aboutrcml#paragraph-146)).

For working with the database, the *SQLite* database engine (http://www.sqlite.org/) is embedded. Consequently, there is no distinction between the rights and access to the database. Therefore to avoid conflicts during access to the database, all software that is different from *RCML*, including the robot choosing modules, should contact the database only in read mode, without modifying the data.

It is important to note that the statistics is written by *RCML* in *runtime* mode, i.e. constantly, therefore, caching DB queries is not recommended in order to avoid losing data accuracy.

### 15.2 The structure of the statistics database

![](http://rcml.info/images/1.png)

Fig. 7 provides a graphic representation of the database statistics structure.

The main table storing the statistics about executing functions by robots is the *function_calls* table. Each record in this table provides information about a particular robot executing a particular function during *RCML* running.

The *function_calls* table has the following fields:

* *id* - unique record ID, primary key;
* *robot_id* - identifier of the robot that performed particular function, an external key referring to field in table *robot_uids*;
* *function_id* - identifier of the performed function, external key that refers to field in table functions;
* *run_id* - RCML program run I, external key referring to field *id* in table *runs*;
* *start* - start of function timestamp, number of microseconds since the start of *RCML* program execution with the called function described by this record; and
* *end* - end of function timestamp, number of microseconds since the start of *RCML* program execution.

A program or a library, in which the robot function is called, is called the function execution context in terms of the statistics database, i.e. the location of calling the function that determines the conditions for its execution.

The table that stores information about contexts is called "contexts" and contains the following fields:

* *id* - unique context ID, primary key;
* *filename* - the name of the context file;
* *hash* - the hash of the context file in the form of a string representation of binary data;
* *iid* - the interface ID of the context presented as a string, only used for the contexts that are *RCML* libraries; and
* *version* - context version, only indicated for the contexts that are *RCML* libraries.

The *functions* table represents the position of robot's specific function call in the context, and has the following fields:

* *id* - a unique identifier of function callsite, primary key;
* *context_id* - identifier of the context in which the function is called, foreign key, refers to the id field in the *contexts* table;
* *name* - the called function name; and
* *position* - the unique callsite of this function in the context. This property should not be confused with the number of the line where this function is called in the source code of the *RCML* program;

The *runs* table logs invocation of those contexts of functions, which are *RCML* programs. This table contains:

* *id* - the unique ID of the context invocation, primary key;
* *context_id* - identifier of the context in which the function is called, foreign key, refers to the id field in the contexts table; and
* *run_at* - timestamp of the *RCML* program invocation, showing the number of microseconds elapsed since epoch 1900.01.01 00:00:00.000.000 in the computer's local time zone.

In addition to the context features, there are sources of the functions similar to functions sources in section ["Calling Functions"](http://rcml.info/eng/aboutrcml#paragraph-164). A function source is an *RCML* program or module (in this case, a module of the robot) that stores the code of called function. With that, the RCML program can be both the source and the context of functions, since it may contain robots' features that call other functions of robots.

The table detailing the function sources is called "sources", and contains the following fields:

* *id* - the unique source ID, primary key;
* *type* - the source type:
   * *1* - RCML library;
   * *2* - robot module.
* *hash* - the hash of the source file in the form of a string representation of binary data;
* *iid* - the interface ID of the source in the form of a string; and
* *version* - version of the source of functions.

The list of robots involved in execution of invoked functions is stored in the *robot_uids* table with the following fields:

* *id* - the unique robot ID, primary key;
* *source_id* - source ID, in this case, the source may only be the module of robots, foreign refers to field "id" in table *sources*; and
* *uid* - robot name that should recommended unique, but may be non-unique.
***

# 16 List of Abbreviations

Below you can find a list of abbreviations in this manual in the order they are first mentioned in the text:

**RCML** – Robot Control Meta Language;

**API** – Application Programming Interface;

**DB** – Database;

**SW** – Software;

**OS** – Operating system;

**PC** – Pseudo Code.
***
# 17 Examples of connecting modules to RCML

RCML allows to cooperate different robots within the same task. Thus, it is possible to implement simultaneous execution of several tasks by a group of robots, and switch a single robot between several tasks.

Solutions for technological tasks implemented in the RCML language ensure the same result for robots with identical functionality.

The RCML environment has a number of I/O points (*API*) for integration with software products and systems, including robots. The functionality of the language itself may be further extended by adding new functions through connecting the modules.

![](http://rcml.info/images/aboutrcml/51ac35fbc4ca5612c521b25948409556.png)

RCML interaction with control and execution devices

* [Robot modules](http://rcml.info/eng/aboutrcml#paragraph-147)
* [Function modules](http://rcml.info/eng/aboutrcml#paragraph-148)
* [Control modules](http://rcml.info/eng/aboutrcml#paragraph-149)

Next, we will focus on examples of connecting each module type in more detail.

### 17.1 An example of connecting robot modules

### 17.1.1 Quick connection of robot test module to RCML

The *test* robot module is intended for imitating a physical robot and testing connected components and programs.

To enable the test module robot, add the *"module"* property to section *[robot_modules]* of the RCML configuration file, and set it equal to the *"test"* value in accordance with section ["Installation and Configuration of RCML Compiler and Interpreter"](http://rcml.info/eng/aboutrcml#paragraph-146). The *test* module does not require additional configuration.

The following is the text of a program in the RCML language. The program invokes the test robot, and sends the command to run the *do_something()* function. The following describes in more details what this function means
```
function main() {
	@lr = robot_test; 
	@lr->do_something(1000);
}
```
During the execution of this program, the test robot imitates physical execution of an action that would take 1 second.

After connecting the module, the following options are available for the programmer:

| Name  | Number of parameters | Returns a value |Throws an exception|Description   |
|:------------- |:------------------:|:------------------:|:------------------:|:------------------|
|none|0|No|No|A function doing nothing.|
|do_something|1|No|No|As the parameter, the function takes the number of milliseconds that the execution of the program will be stopped for.|
|get_some_value|1|Yes|No|Returns the value passed as the input parameter.|
|throw_exception|0|No|Yes|When invoked, throws an exception.|
|print|2|No|No|Accepts the first string parameter and prints it in the console after the delay specified in milliseconds by the second parameter.|

In addition, the test robot also has axes:

| Name  | The upper limit |The lower limit | Note |
|:------------- |:------------------:|:------------------:|:------------------:|
|X|100|-100||
|Y|1|0|Binary|
|Z|100|0|||

### 17.1.2 Quick connection of the lego_ev3 module to RCML

![](http://rcml.info/images/aboutrcml/16_1_2_1.png)

*Lego Mindstorms Ev3* is the third generation of the *Lego kit*.

More information is available from the official website of the kit:

![](http://rcml.info/images/aboutrcml/16_1_2_2.png)

The module of the *lego_ev3* robot is used for connecting *Lego Ev3* units to RCML. 

1. To activate a *lego_ev3* module, add entry "property *module*" to section *[robot_modules]* to the RCML configuration file, and set it equal to *lego_ev3* in accordance with Section ["Installation and Configuration of RCML Compiler and Interpreter"](http://rcml.info/eng/aboutrcml#paragraph-146).
2. The *Bluetooth* adapter should be paired with the *Lego Ev3* module.
3. After pairing, go to the properties of the added device. You should know the address of the *COM* port with which interaction occurs.

![](http://rcml.info/images/aboutrcml/4ab548bcfd1fe132ec2ed908fcc749d1.png)

* Next, you should configure the module. Namely, you should specify, which *COM* port should communicate with the *Ev3* module. Go to the directory with therobot modules (*robot_modules*), and then to the directory of the *Ev3* module (*lego_ev3*).
* In the directory of the *lego_ev3* module, create file *config.ini* with the following content:
```
[connections]
connection = COM6
[options]
dynamic_connection = 0
```

Configuration file *config.ini* contains the following properties divided into two sections *[connections]* and *[options]*:

The *[connections]* section contains the numbers of ports of the connected robots; for each robot, a record is made as follows: 

*connection = COMX*, where *X* is the number of the port that is used in the properties of the connection.

Section *[options]* contains property *dynamic_connection*.

*dynamic_connection* is used to dynamically connect robots, i.e., it allows to change the number of the used robots immediately at the time of program execution. However, each activated robot takes time to connect to it. If the *dynamic_connection* value is set to 1, then the *Bluetooth* connection with the robot will be established at the time of the robot invocation, and disconnected after it is released, which may cause significant delays in using the robot in the RCML program.

If the value of *dynamic_connection* is set to 0, the *Bluetooth* connection with the robot will be established at the moment the program starts, and disconnected after completion.

By default, the value will be set to 0.

The following is an example text of a program in the RCML language. A program invokes the *Lego* robot and interacts with the motor connected to the *Ev3* unit. The text of the program is the following:
```
function main() {
	@lr = robot_lego_ev3;
	@lr->motorMoveTo("B",10,200,0);
}
```
The first line creates a variable the *Lego* robot will be associated with.

The second line will move the motor *B* to position *200* at speed *10* without using the brake.

Besides, the *Lego* robot may be presented to RCML as *"Track Vehicle"*, which will allow to address two motors of the *Lego* robot as a whole, and provides additional control functions. The *setTrackVehicle* function has been specially created for robots of special configurations, with two drive wheels and one swivel wheel/prop, or for configurations of tracks-type robots, 1 motor per each wheel or caterpillar. This mode automates motors control for maneuvering the robot. To initialize the robot in the *"Track Vehicle"* mode, use the *setTrackVehicle* function in the program.

The following is an example of program text in the RCML language, where *"Track Vehicle"* is created and controlled.
```
function main() {
	@lr = robot_lego_ev3; 
	@lr->setTrackVehicle("B","C",1,1);
	@lr->trackVehicleForward(10);
	system.sleep(1000);
	@lr->trackVehicleOff();
}
```
As a result of executing the program, the robot will symmetrically rotate both motors connected to *B* and *C* ports at speed *10*. A more detailed list of commands is provided below.

If the program code does not invoke the *"Track Vehicle" setTrackVehicle* function, then an exception will be thrown when functions related to the use of *"Track Vehicle"* are executed; this exception may be handled with the *try/catch* construct. More information about it is available in Section ["Exceptions"](http://rcml.info/eng/aboutrcml#paragraph-163).

There are peculiarities associated with the input values for the functions of the module.

To invoke motors A,B,C,D, characters "A","B","C" and "D" are used, respectively;

To invoke sensors numbered 1,2,3,4, corresponding numbers are used;

Parameters of speed and percent functions have the following limitations:

* *speed* takes values *[-100;100]*;
* *percent* takes values *[0;100]*.

When these limits are exceeded, the called function throws an exception that can be processed using a construct in the RCML language. More detail about exception handling is available in Section ["Exceptions"](http://rcml.info/eng/aboutrcml#paragraph-163).

The available functions of the robot are shown in the Table:

| Definition  | Description |Example |
|:------------- |:------------------|:------------------|
|motorSetSpeed(motor,speed)|Sets speed "speed" for motor "motor"|@lr->motorSetSpeed ("B",20); Sets speed 20 for motor B.|
|motorOff(motor)|Disables the motor without using the brake, i.e., the motor stops immediately.|@lr->motorOff("C"); Will stop motor C.|
|motorBrake(motor)|Stops the motor using the brake. Immediate stop.|@lr->motorBrake("D"); Will stop motor D.|
|motorSetDirection(motor, direction)|Sets motor direction. direction=0 sets motor direction "backward". direction != 0 sets motor direction to "forward".|@lr->motorSetDirection("A",0); Will set motor A direction to "backward".|
|motorGetDirection(motor)|Returns 1 if motor direction is set to "forward"; returns 0 if motor direction is set to "backward". The motor will not stop immediately.|RecievedDirection = @lr->motorGetDirection("C");|
|motorMoveTo(motor,speed,position,brake)|Moves the motor to position "position" at speed "speed". brake = 0 - brake not to be used, brake != 0 - brake to be used.|@lr->motorMoveTo("B",10,200,0); Will move motor B to position 200 at speed 10 without using the brake.|
|motorGetTacho(motor)|Returns motor position.|RecievedTacho = @lr->motorGetTacho("C");|
|motorResetTacho(motor)|Sets current motor position to zero.|@lr->motorResetTacho("C");|
|waitMotorToStop(motor)|Waits until the motor stops.|@lr->waitMotorToStop("C");|
|waitMultiMotorsToStop(motorA,motorB,motorC,motorD)|Waits until several motors stop. If motor != 0, waits for the stop, if motor = 0, does not wait.|@lr->waitMultiMotorsToStop(1,0,0,1); Waits until the motors A and D stop.|
|setTrackVehicle(motorLeft,motorRight,LeftReverse,RightReverse)|Logically connects 2 motors - motorLeft and motorRight in Track Vehicle (tracked vehicles). Allows to invoke these motors as a whole, and provides additional Track Vehicle control functions with prefix "trackVehicle". If the value is != 0, Left and Right Reverse sets motors' reverse direction.|@lr->setTrackVehicle("B","C",1,0); Logically connects the motors B and C in Track; motor B changes its direction to the opposite.|
|trackVehicleBackward(speed)|Track Vehicle moves backward at speed "speed".|@lr->trackVehicleBackward(20);|
|trackVehicleForward(speed)|Track Vehicle moves forward at speed "speed".|@lr->trackVehicleForward(40);|
|trackVehicleOff()|Disables Track Vehicle motors. The motors will not stop immediately.|@lr->trackVehicleOff();|
|trackVehicleBrake()|Stops the Track Vehicle motors using a brake.|@lr->trackVehicleBrake();|
|trackVehicleSpinLeft(speed)|Track Vehicle turns left around its vertical axis.|@lr->trackVehicleSpinLeft(10);|
|trackVehicleSpinRight(speed)|Track Vehicle turns right around its vertical axis.|@lr->trackVehicleSpinRight(90);|
|trackVehicleTurnLeftForward(speed,percent)|Track Vehicle turns left, moving forward. The right motor will be set to speed "speed", and the left one to (speed - speed*percent/100). Therefore, if percent=0, the motors will have the same speed, and if percent=100, the left engine will have zero speed.|@lr->trackVehicleTurnLeftForward(20, 50);|
|trackVehicleTurnRightForward(speed,percent)|Track Vehicle turns left, moving forward. The left motor will be set to speed "speed", and the right one to (speed - speed*percent/100). Therefore, if percent=0, the motors will have the same speed, and if percent=100, the tight engine will have zero speed.|@lr->trackVehicleTurnRightForward(10, 90);|
|trackVehicleTurnLeftReverse(speed,percent)|Track Vehicle turns left, moving backward. The right motor speed is set to "speed", and the left motor one to (speed - speed*percent/100).|@lr->trackVehicleTurnLeftReverse(10,20);|
|trackVehicleTurnRightReverse(speed,percent)|Track Vehicle turns right, moving backward. The left motor speed is set to "speed", and the right motor one to (speed - speed*percent/100).|@lr->trackVehicleTurnRightReverse(50,50);|
|readSensor(sensor,mode)|Reads sensor No. "sensor" indications in the mode "mode". For different sensors, there are different numbers of modes.|@lr->readSensor(2,1); Reads indications of the sensor connected to port 2 in mode 1.|
|isMotorRunning(motor,sleep)|Returns 1 if motor "motor" is enabled, and 0 if it is not. If sleep is not 0, the motor will be polled with a 200 microseconds delay.|@lr->isMotorRunning("B",1);|
|getMotorSpeed(motor,sleep)|Returns the speed value of motor "motor". If sleep is not 0, the motor will be polled with a 200 microseconds delay.|@lr->getMotorSpeed("B",1);|

For the *lego_ev3* module, connection of 8 types of sensors is available. Their indices are used in commands of the RCML language. Types of sensors and their indices are shown in the table:

| Sensor | Mode |
|:------------- |:-----------|
|Ultrasonic|1 - the mode of measuring the distance in centimeters 2 - the mode of measuring the distance in inches 3 - listening mode|
|Infrared|1 - the mode of measuring the distance 2 - the mode of locating the infrared remote control 3 - the mode of detecting button actions on the infrared remote control|
|Color|1 - the mode of defining light intensity 2 - the mode of defining color (see the color table) 3 - mode of defining raw value of the reflected light 4 - the mode of defining reflected light
|Gyroscopic|1 - mode of defining the angle in degrees 2 - the mode of defining angular velocity in degrees per second|
|Pressing sensor|1 - pressing detection mode 2 - the mode of displaying the number of pressings|
|Temperature sensor|1 - the mode of measuring temperature in degrees Celsius 2 - the mode of measuring temperature in degrees Fahrenheit|
|Sound sensor|1 - the mode of measuring sound volume in dB 2 - the mode of measuring sound volume in dBA|
|Light sensor|1 - mode of measuring intensity 2 - the mode of measuring reflected light|

The color sensor can return the color code. The returned color codes are shown in the Table:

| Color| Return value |
|:-------------:|:------------------:|
|Black|1|
|Blue|2|
|Green|3|
|Yellow|4|
|Red|5|
|White|6|
|Brown|7|

The following axes are always available for controlling the *Ev3* unit:

| Axis| The upper limit |The lower limit|Description|
|:-------------|:------------------:|:-------------:|:------------------|
|locked|1|0|Binary axis. If its value is 1, the robot is considered blocked and ignores changes in the values in any other axes.|
|speedMotorA|100|-100|The value of the axis sets the speed of the motor connected to port A.|
|speedMotorB|100|-100|The value of the axis sets the speed of the motor connected to port B.|
|speedMotorC|100|-100|The value of the axis sets the speed of the motor connected to port C.|
|speedMotorD|100|-100|The value of the axis sets the speed of the motor connected to port D.|
|moveMotorA|1,000|-1,000|The axis value specifies the position that motor A will turn at speed 50.|
|moveMotorB|1,000|-1,000|The axis value specifies the position that motor B will turn at speed 50.|
|moveMotorC|1,000|-1,000|The axis value specifies the position that motor C will turn at speed 50.|
|moveMotorD|1,000|-1,000|The axis value specifies the position that motor D will turn at speed 50.|

If the *Lego* robot is used in the *"Track Vehicle"* mode, the robot has the following axes, to which it can connect for control:

|||||
|---|:---:|:---:|---|
|straight|100|-100|The axis value specifies the speed at which *"Track Vehicle"* moves straightforward|
|rotation|100|-100|The axis value specifies the speed at which *"Track Vehicle"* rotates around its vertical axis.|

The *Lego* robot has to be initialized in the *"Track Vehicle"* mode before straight axis or rotation is activated, i.e., before activating manual control!

### 17.1.3 Quick connection of the lego_nxt module to RCML

![](http://rcml.info/images/aboutrcml/16_1_3_1.png)

*Lego Mindstorms NXT* is the second generation of the *Lego kit*.

More information is available from the official website of the kit: 

![](http://rcml.info/images/aboutrcml/16_1_3_2.png)

The module of the *lego_nxt* robot is used for connecting *Lego NXT* units to RCML. 

1. To activate the *lego_nxt* module, add entry "property *module"* to section *[robot_modules]* in the RCML configuration file, and set it equal to *lego_nxt* in accordance with Section ["Installation and Configuration of RCML Compiler and Interpreter"](http://rcml.info/eng/aboutrcml#paragraph-146).
2. The *Bluetooth* adapter should be paired with the *Lego NXT* module.
3. After pairing, go to the properties of the added device. You should know the address of the *COM* port with which interaction is established.

![](http://rcml.info/images/aboutrcml/c66546c5998957cc2f1f1574d2c75e0e.png)

1. Next, configure the module. Namely, specify which *COM* port should communicate with the *NXT* module. Go into the directory with the robotmodules *(robot_modules)*, and then to the directory of the NXT module *(lego_nxt)*.
2. In the directory of the *lego_nxt* module, create the *config.ini* file with the following content:
```
[connections]
connection = COM6
[options]
dynamic_connection = 0
```
The *config.ini* configuration file contains the following properties divided into two sections *[connections]* and *[options]*:

The *[connections]* section contains the numbers of ports of connected robots; for each robot, a record is made as follows: 

*connection = COMX*, where *X* is the number of the port that is used in the properties of the connection.

Section *[options]* contains property *dynamic_connection*.

*dynamic_connection* is used to dynamically connect robots, i.e., it allows to change the number of the used robots immediately at the time of program execution. However, each activated robot takes time to connect to. If the *dynamic_connection* value is set to 1, then the *Bluetooth* connection with the robot will be established at the time of the robot invocation, and disconnected after it is released, which may cause significant delays in using the robot in the RCML program.

If the value of *dynamic_connection* is set to 0, the *Bluetooth* connection with the robot will be established at the moment of program start, and disconnected after completion.

By default, the value will be set to 0.

The following is an example text of a program in the RCML language. A program invokes the *Lego* robot, and interacts with the motor connected to the *NXT* unit. The text of the program is the following:
```
function main() {
	@lr = robot_lego_nxt;
	@lr->motorMoveTo("B",10,200,0);
}
```
The first line creates a variable the *Lego* robot will be associated with.

The second line will move the motor *B* to position *200* at speed *10* without using the brake.

Besides, the *Lego* robot may be presented to RCML as *"Track Vehicle"*, which will allow addressing two motors of the *Lego* robot as a whole, and provides additional control functions. The *setTrackVehicle* function has been specially created for robots with special configurations, with two drive wheels and one swivel wheel/prop, or for configurations of tracks-type robots, 1 motor per each wheel or caterpillar. This mode automates motors control for maneuvering the robot. To initialize the robot in the *"Track Vehicle"* mode, use the *setTrackVehicle function* in the program. The following is an example of program text in the RCML language, where *"Track Vehicle"* is created and controlled.
```
function main() {
	@lr = robot_lego_nxt; 
	@lr->setTrackVehicle("B","C",1,1);
	@lr->trackVehicleForward(10);
	system.sleep(1000);
	@lr->trackVehicleOff();
}
```
As a result of executing the program, the robot will symmetrically rotate both motors connected to *B* and *C* ports at speed *10*. A more detailed list of commands is provided below.

If the program code does not invoke the *"Track Vehicle" setTrackVehicle* function, then an exception will be thrown when functions related to the use of *"Track Vehicle"* are executed; this exception may be handled with the *try/catch* construct. More information about it is available in Section ["Exceptions"](http://rcml.info/eng/aboutrcml#paragraph-163).

There are peculiarities associated with the input values for the functions of the module.

To invoke motors A,B,C,D, characters *"A","B","C"* and *"D"* are used, respectively;

To invoke sensors numbered 1,2,3,4, corresponding numbers are used;

Parameters of *speed* and *percent* functions have the following limitations:

* *speed* takes values *[-100;100]*;
* *percent* takes values *[0;100]*.

When these limits are exceeded, the called function throws an exception that can be processed using a construct in the RCML language. More detail about exception handling is available in Section ["Exceptions"](http://rcml.info/eng/aboutrcml#paragraph-163). 

The available functions of the robot are shown in the Table below:

| Definition  | Description |Example |
|:------------- |:------------------|:------------------|
|motorSetSpeedmotor,speed)|Sets speed "speed" for motor "motor"|@lr->motorSetSpeed ("B",20); Sets speed 20 for motor B.|
|motorOff(motor)|Disables the motor without using the brake, i.e., the motor stops immediately.|@lr->motorOff("C"); Will stop motor C.|
|motorBreak(motor)|Stops the motor using the brake. Immediate stop.|@lr->motorBrake("A"); Will stop motor A.|
|motorSetDirection(motor,direction)|Sets the direction of motor "motor". direction=0 sets motor direction "backward". direction != 0 sets motor direction to "forward".|@lr->motorSetDirection("A",0); Will set motor A direction to "backward".|
|motorGetDirection(motor)|Returns 1 if motor direction is set to "forward", returns 0 if motor direction is set as "backward". The motor will not stop immediately.|RecievedDirection = @lr->motorGetDirection("C");|
|motorMoveTo(motor,speed,position,brake)|Moves motor "motor" to position "position" at speed "speed". brake = 0 - brake not to be used, brake != 0 - brake to be used.|@lr->motorMoveTo("B",10,200,0); Will move motor B to position 200 at speed 10 without using the brake.|
|motorGetTacho(motor)|Returns motor position.|RecievedTacho = @lr->motorGetTacho("C");|
|motorResetTacho(motor)|Sets current motor "motor" position to zero.|@lr->motorResetTacho("C");|
|waitMotorToStop(motor)|Waits until the motor stops.|@lr->waitMotorToStop("C");|
|waitMultiMotorsToStop(motorA,motorB,motorC)|Waits until several motors stop. If motor != 0, waits for the stop, if motor = 0, does not wait.|@lr->waitMultiMotorsToStop(1,0,1); Waits until the motors A and C stop.|
|setTrackVehicle(motorLeft,motorRight,LeftReverse,RightReverse)|Logically connects 2 motors - motorLeft and motorRight in Track Vehicle (tracked vehicles). Allows to invoke these motors as a whole, and provides additional Track Vehicle control functions with prefix "trackVehicle". If the value is != 0, Left and Right Reverse sets motors' reverse direction.|@lr->setTrackVehicle("B","C",1,0); Logically connects the motors B and C in Track; motor B changes its direction to the opposite.|
|trackVehicleBackward(speed)|Track Vehicle moves backward at speed "speed".|@lr->trackVehicleBackward(20);|
|trackVehicleForward(speed)|Track Vehicle moves forward at speed "speed".|@lr->trackVehicleForward(40);|
|trackVehicleOff()|Disables Track Vehicle motors. The motors will not stop immediately.|@lr->trackVehicleOff();|
|trackVehicleBrake()|Stops the Track Vehicle motors using a brake.|@lr->trackVehicleBrake();|
|trackVehicleSpinLeft(speed)|Track Vehicle turns left around its vertical axis.|@lr->trackVehicleSpinLeft(10);|
|trackVehicleSpinRight(speed)|Track Vehicle turns right around its vertical axis.|@lr->trackVehicleSpinRight(90);|
|trackVehicleTurnLeftForward(speed,percent)|Track Vehicle turns left, moving forward. The right motor will be set to speed "speed", and the left one to (speed - speed*percent/100). Therefore, if percent=0, the motors will have the same speed, and if percent=100, the left engine will have zero speed.|@lr->trackVehicleTurnLeftForward(20, 50);|
|trackVehicleTurnRightForward(speed,percent)|Track Vehicle turns left, moving forward. The left motor will be set to speed "speed", and the right one to (speed - speed*percent/100). Therefore, if percent=0, the motors will have the same speed, and if percent=100, the right engine will have zero speed.|@lr->trackVehicleTurnRightForward(10, 90);|
|trackVehicleTurnLeftReverse(speed,percent)|Track Vehicle turns left, moving backward. The right motor speed is set to "speed", and the left motor one to (speed - speed*percent/100).|@lr->trackVehicleTurnLeftReverse(10,20);|
|trackVehicleTurnRightReverse(speed,percent)|Track Vehicle turns right, moving backward. The left motor speed is set to "speed", and the right motor one to (speed - speed*percent/100).|@lr->trackVehicleTurnRightReverse(50,50);|
|isMotorRunning(motor,sleep)|Returns 1 if motor "motor" is enabled, and 0, if it is not. If sleep is not 0, the motor will be polled with a 200 microseconds delay.|@lr->isMotorRunning("B",1);|

The following options are available for the *lego_nxt* module:

| Definition  | Description |Modes|
|:------------- |:------------------|:------------------|
|readHiTecColor (port, mode)|Reads HiTecColor sensor indications at port "port" in mode "mode".|1 - Returns the red color value [0;255] 2 - Returns the green color value [0;255] 3 - Returns the blue color value [0;255]|
|readHiTecCompass (port)|Reads indications of the HiTecCompass sensor at port "port".|Returns the value in degrees|
|readHiTecGyro (port)|Reads indications of the HiTecGyro sensor at port "port".|Returns angular acceleration in degrees per second|
|readHiTecTilt (port, mode)|Reads HiTecTilt sensor indications at port "port" in mode "mode".|1 - Returns an integer position of the sensor along the x-axis 2 - Returns an integer position of the sensor along the y-axis 3 - Returns an integer position of the sensor along the z-axis|
|readNXTColor (port, mode)|Reads NXTColor sensor indications at port "port" in mode "mode".|1 - Returns an integer value corresponding to the color table 2 - Returns intensity of the red color in percent 3 - Returns intensity of the green color in percent 4 - Returns intensity of the blue color in percent 5 - Returns light intensity in percent|
|readNXTLight (port, mode)|Reads NXTLight sensor indications at port "port" in mode "mode".|1 - Returns light intensity as an integer value 2 - Returns light intensity in percent|
|readNXTSonar (port, mode)|Reads NXTLight sensor indications at port "port" in mode "mode".|1 - the mode of measuring the distance in centimeters 2 - the mode of measuring the distance in inches|
|readNXTSound (port,mode)|Reads NXTSound sensor indications at port "port" in mode "mode".|1 - the mode of measuring sound volume in dB 2 - the mode of measuring sound volume in dBA|
|readNXTTouch (port,mode)|Reads NXTTouch sensor indications at port "port" in mode "mode".|1 - the pressing detection mode 2 - the mode of displaying the number of pressings|
|readRCXLight (port, mode)|Reads RCXLight sensor indications at port "port" in mode "mode".|1 - the mode of measuring intensity 2 - the mode of measuring reflected light|
|readRCXRotation (port)|Reads indications of the RCXRotation sensor at port "port".|Returns rotation increment [-16;+16]|
|readRCXTemperature (port, mode)|Reads RCXTemperature sensor indications at port "port" in mode "mode".|1 - the mode of measuring temperature in degrees Celsius 2 - the mode of measuring temperature in degrees Fahrenheit|

The color sensor can return the color code. The returned color codes are shown in the Table:

|Color | Return value |
|:------------- |:------------------:|
|Black|1|
|Blue|2|
|Green|3|
|Yellow|4|
|Red|5|
|White|6|

The following axes are always available for controlling the *NXT* unit:

|Axis | The upper limit |The lower limit|Description|
|:-------------|:------------------:|:------:|:------|
|locked|1|0|Binary axis. If its value is 1, the robot is considered blocked and ignores changes in the values in any other axes.|
|speedMotorA|100|-100|The value of the axis sets the speed of the motor connected to port A.|
|speedMotorB|100|-100|The value of the axis sets the speed of the motor connected to port B.|
|speedMotorC|100|-100|The value of the axis sets the speed of the motor connected to port C.|
|moveMotorA|1,000|-1,000|The axis value specifies the position that motor A will turn at speed 50.|
|moveMotorB|1,000|-1,000|The axis value specifies the position that motor B will turn at speed 50.|
|moveMotorC|1,000|-1,000|The axis value specifies the position that motor C will turn at speed 50.|

If the *Lego* robot is used in the *"Track Vehicle"* mode, the robot has the following axes, to which it can connect for control:

|Axis | The upper limit |The lower limit|Description|
|:-------------|:------------------:|:------:|:------|
|straight|100|-100|The axis value specifies the speed at which Track Vehicle moves straightforward|
|rotation|100|-100|The axis value specifies the speed at which "Track Vehicle" rotates around its vertical axis.|

The *Lego* robot has to be initialized in the *"Track Vehicle"* mode before straight axis or rotation is activated, i.e., before activating manual control!

### 17.1.4 Quick connection of uarm module to RCML

![](http://rcml.info/images/aboutrcml/16_1_4_1.jpg)

*uArm* is a 4-axis manipulator, a miniature version of the industrial *ABB PalletPack IRB460* robot. 

More information is available from the official website of the robot:

![](http://rcml.info/images/aboutrcml/16_1_4_2.png)

The module of uarm is used for connecting the uArm robot to RCML.

1. The *uArm* robot should be connected to the computer. First, visit the [Download Center](https://ufactory.cc/en/download-center/) section at the manufacturer's website, then go to *Instructions*, and go to *Getting Started*, after which follow the instruction for the suitable uArm model.
2. Next, you have to change the robot software to work with *SDK C#*. [Quick start instruction for working with SDK C#](http://developer.ufactory.cc/quickstart/csharp/).
3. To activate a *uarm* module, add entry "property *module*" to section *[robot_modules]* to the RCML configuration file, and set it equal to uarm in accordance with Section ["Installation and Configuration of RCML Compiler and Interpreter"](http://rcml.info/eng/aboutrcml#paragraph-146).
4. Next, you should configure the module. Namely, you should specify which *COM* port should communicate with the robot. To do so, go into the directory with the robot modules *(robot_modules)*, and then to the directory of the uarm module.
5. In the directory of the *uarm* module, create file *config.ini* with the following content:
```
[main]
count_robots = 1
is_debug = 0
robot_port_0 = com3
```
The module configuration file consists of section *[main]*, which includes the following properties:

* *count_robots* is the property that takes as the parameter the number of *uArm* robots (number) to which it should connect.
* *is_debug* displays additional information about functions executable by the *uArm* robot in the console. May take value 0 or 1.
* *robot_port_X* contains the name of the *COM* port that robot X is connected to; the robots count is zero-based. The port address should be specified in the *comY* format, where *Y* is the serial port number that was previously used in par. 1 and 2.

The following is the text of a program in the RCML. The program invokes the *uArm* robot and activates the vacuum pump for 5 seconds, then deactivates it:
```
function main() {
	@ur = robot_uarm;
	@ur-> pump_on();
	system.sleep(1000);
	@ur-> pump_off();
}
```
Available functions of the robot are shown in the Table:

|Definition|Description|Example|
|:------------- |:------------------|:------|
|move_to(X, Y, Z)|moves the robot grip to the point with coordinates X, Y, Z. The motion occurs at the maximum possible speed.|@Robot->move_to (10, 10, 10); moves the robot grip to the point with coordinates X=10, Y=10, Z=10|
|pump_on()|activates the grip pump|@Robot->pump_on(); activates the grip pump|
|pump_off()|deactivates the grip pump|@Robot->pump_off(); deactivates the grip pump|
|find_x()|returns the X coordinate of the point the robot grip is located at|X = @Robot->find_x(); gets the X coordinate of the point the robot grip is located at, and stores it to variable X|
|find_y()|returns the Y coordinate of the point the robot grip is located at|Y = @Robot->find_y(); gets the Y coordinate of the point the robot grip is located at, and stores it to variable Y|
|find_z()|returns the Z coordinate of the point the robot grip is located at|Z = @Robot->find_z(); gets the Z coordinate of the point the robot grip is located at, and stores it to variable Z|
|move_to_at_once(x,Y, Z)|moves the robot grip to the point with coordinates X, Y, Z. The motion occurs at the maximum possible speed.|@Robot->move_to_at_once (10, 10, 10); moves the robot grip to the point with coordinates X=10, Y=10, Z=10|
|move(x, Y, Z)|moves the robot grip from the point with current coordinates X_current, Y_current, Z_current to the point with coordinates X_current + X, Y_current + Y, Z_current + Z. The movement occurs smoothly.|@Robot->move(1, 1, 1); changes the coordinates of the point where the robot grip is located to the point with specified values (X_current + 1, Y_current + 1, Z_current + 1)|
|move_to_1(x, Y, Z,T)|moves the robot grip to the point with coordinates X, Y, Z with delays T between intermediate steps. Time is specified in milliseconds|@Robot->move_to_1(1, 1, 1, 50); moves the robot grip to the point with coordinates X=1, Y=1, Z=1, with 50 millisecond delays between intermediate steps.|
|move_to_2(x, Y, Z,T, R, Th4)|moves the robot grip to the point with coordinates X, Y, Z with delays T between intermediate steps. Also rotates the robot grip around the axis by Th4 degrees|@Robot->move_to_2(1, 1, 1, 1, 1, 30); moves the robot grip to the point with coordinates X=1, Y=1, Z=1, with 50 millisecond delays between intermediate steps. Will also rotate the robot grip around the axis by 30 degrees|
|write_angles(Th1,Th2, Th3, Th4)|changes the angles of the servo-drives by values Тh1, Th2, Th3, and Th4|@Robot->write_angles(10, 10, 10, 10); will change the angles of the servo-drives by 10 degrees|
|rotate_servo4(Th4)|will change the angle of robot grip servo-drive position by specified value Тh4|@Robot->rotate_servo4(15); will change the angle of robot grip servo-drive position by 15 degrees|

The following axes for controlling *uArm* robot are available:

|Axis | Upper limit |Lower limit|Description|
|:-------------:|:------------------:|:------:|:------|
|locked|1|0|Binary axis. If its value is 1, the robot is considered blocked and ignores changes in the values in any other axes.|
|pump|1|0|Binary axis. If its value is 1, the robot pump will be enabled. If the value is 0, the pump will be disabled.|
|servo1|180|0|The axis of changing the angle of the first servo located in the base.|
|servo2|180|0|The axis of changing the angle of the second servo acting on the angle between the base and the lower shoulder of the robot.|
|servo3|180|0|The axis to changing the angle of the third servo - between the shoulders of the robot.|
|servo4|180|0|The axis of changing the angle of the fourth servo that changes the angle of grip rotation.|

### 17.1.5 Quick connection of smc module to RCML

The module of the *smc* robot allows in *RCML* to control *LEH* series industrial electromechanical grips manufactured by *SMC*, using the *LECP6* controller of the same manufacturer. Each grip is presented in *RCML* as a robot that may be used and released afterwards. The *LECP6* controller is connected to a *PC* running *RCML* via the proprietary cable that comes with the *SMC Controller setting kit LEC-W2* used to control gripping from the *PC*.

![](http://rcml.info/images/LE.jpg)

*Industrial LEH series electromechanical grips manufactured by SMC*

![](http://rcml.info/images/2.jpg)

*Standard LECP6 controller for controlling LEH series grips*

These grips allow programming the travel and force of the jaws. More information about the *LEH* series grips is available at the official website:

![](http://rcml.info/images/smc.png)

To control an *SMC LEH* series grip via *RCML*, one has to:

1. Install and connect the grip to the controller according to the manufacturer's instructions.
2. Connect the controller to the PC.
3. Add an entry into section *[robot_modules]* of the *RCML* configuration file: property module, and set it equal to smc in accordance with section ["Installation and Configuration of RCML Compiler and Interpreter"](http://rcml.info/eng/aboutrcml#paragraph-146).
4. Next, configure the module. Specify which *COM* port should communicate with the grip controller. To do so, go into the directory with the robot modules *(robot_modules)*, and then to the directory of the *SMC* module.
5. In the directory of the *smc* module, create file *config.ini* with the following content:
```
[main]
count_robots = 1
is_debug = 0
robot_port_0 = \\.\\COM3
```

The module configuration file consists of section [*main*], which includes the following properties:
* *count_robots* is the property that takes as the parameter the number of *SMC* controllers to which it should connect.
* *is_debug* displays in the console the data packages sent to the *LECP6* controller. It may take value 0 or 1.
* *robot_port_X* – contains the name of the *COMport* to which robot *X* is connected. The port address is to be specified in format \ \ .\ \  *COMY*, where *Y* is the number of the port (0-based enumeration).

The following is the text of a program in the *RCML* language. The program calls the *SMC* grip, sends a command to separate the jaws by 1 cm at the speed of 1 mm/sec, and then sends the command to return to the default position:
```
function main() {
 @r = robot_smc;
 @r-> move_to(1000, 1);
 @ur->return_to_origin();
}
```
The available functions of the module are shown in the Table:

|Definition | Description |	Example|
|:------------- |:------------------|:------|
|move_to(pos, spd)|Sets the distance between the jaws of the grip to pos (in hundredths of a millimeter). The jaws of the grip move at the speed of spd (mm/sec)|@Robot->move_to (200, 10); sets the distance between the jaws of the grip equal to 2 mm. The jaws of the grip move at the speed of 10 mm/sec|
|return_to_origin()|Returns the jaws of the grIp to the default position (1 mm distance between the jaws)|@Robot->return_to_origin();|
|get_cur_position()|Restores the current distance between the jaws of the grip (hundredths of a millimeter)|@Robot-\>get\_cur\_position();|
|get_cur_speed()|Returns the current speed of grip jaws movement in mm/sec|X = @Robot->find_x(); gets the X coordinate of the point the robot grip is located at, and stores it to variable X;|
|get_cur_thrust()|Returns the current load value (%)|thrust = @Robot->get_cur_thrust();|
|move_to2( move_mode, spd, pos, accel, decel, pushing_force, trigger_level, pushing_speed, moving_force, area_output1, area_output2, in_position)|Sets the distance between the jaws of the grip to pos (hundredths of a millimeter); move_mode - movement type (1 - absolute movement, 2 - relative movement); spd - speed (mm/s), accel – acceleration at the start of the movement (mm/s^2); decel – deceleration at stop (mm/s^2); pushing_force (0 - positioning, [1-100] - effect with specified torque (%); trigger_level – the value of the effect, at which INP flag will become 1; pushing_speed - speed of movement at effect (mm/s); moving_force – the maximum force during movement (%); area_output1, area_output2 - if the current position is between the specified values, flag AREA will become 1; in_position - if the current position coincides with the specified value, INP flag will take 1||
|set_hand_control_params(spd, accel, decel)|Sets spd speed in mm/sec, acceleration accel in mm/s^2 and deceleration decel in mm/s^2 for manual control of the grip|@Robot->set_hand_control_params(20, 50, 50);|

Available axes for the manual control mode:

|Axis | The upper limit|The lower limit|Description|
|:------------- |:------------------|:------|:------|
|locked|1|0|Binary axis. If its value is 1, the grip is considered blocked and ignores changes in the values in any other axes.|
|servo1|1000|100|The axis that sets the distance between grip jaws.|

### 17.1.6 Quick connection of FANUC module to RCML

For operating *FANUC* industrial robots with *RCML*, there is a cognominal robot module, *FANUC*. This module is compatible with production controllers of the *A-cabinet* and the *B-cabinet* series with enabled *Modbus TCP software option*. Therefore, the *FANUC* module supports all robots that are compatible with these controllers.

![](http://rcml.info/images/fanuc.jpg)

Read more about *FANUC* robots on the official website of the company:

![](http://rcml.info/images/fanuc.png)

This module can control the robot from a PC via *RCML*, and set in runtime arbitrary trajectories of the robot, including support of controlling additional groups of axes (rotary table, etc.)

The low-level functionality of the *FANUC* module is the ability to send various data types to the controller from *RCML*: bits, integers, real numbers, and program invocation using the *PNS* mechanism.

The PC with *RCML* communicates with the *FANUC* controller via an *FTP Ethernet* cable. The robot, the controller and the PC with *RCML* should be appropriately configured beforehand. The information about the setup procedure and preparation is available on request. Send you free form request to info@robotct.ru. 

To connect the *FANUC* module, perform the following steps:

1. Perform the required initial configuration of the robot and the controller;
2. Configure the PC with *RCML* for establishing communication with the controller;
3. Connect the *FANUC* controller to the PC with *RCML* via an *FTP Ethernet* cable; if there are several robots, they may be connected into the same network using appropriate network equipment.
4. Add an entry into section *[robot_modules]* of the *RCML* configuration file: property *module*, and set it equal to *fanuc* in accordance with section ["Installation and Configuration of RCML Compiler and Interpreter"](http://rcml.info/eng/aboutrcml#paragraph-146).
5. Next, configure the *FANUC* module itself; to do that, create a file named *config.ini* in the module directory, and fill it according to the rules below.

The configuration file should always contain section "main" with property *count_robots*, which indicates the number of the controlled *FANUC* robots.

For each controlled robot, a section is created with name like *robot_X*, where *X* is the serial number of the robot, countdown starts with 1. This section is called "robot section". The robot section should contain 4 properties:

* *robot_name* - the unique display name of the robot, string;
* *ip* – the IP address of the robot controller in the network, string;
* *port* - the port of the robot, digit; and
* *slave_id* - the number of the robot in the *Modbus* network.

It is expected that all robots connected to *RCML* will have the same configuration of digital inputs and outputs, *DI* and *DO*, respectively. For configuring DI, a homonymous section is used, where the sent data cells and their types may be specified. Each cell has a display name used in the *RCML* program for reference to that cell.

The following data types are supported:

* Boolean. Specified as *logic_%name% = X*, where %name% is the cell name, and X is the input number. This type involves only 1 input.
* Integer. Specified as *integer_%name% = X*, where *%name%* is the cell name, and X is the number of the input where the counting of positions starts for sending the number. This type has the size of 16 bits and, accordingly, it uses 16 inputs. With that, counting of positions may use only those inputs the numbers of which, when divided by 16, give 1 in residuo (1, 17, 33, 49, etc.). The integer type can transmit only non-negative integers.
* Real. It is specified as *real_%name%* = A,B,C, where *%name%* is the cell name, and A is the number of the input used for sending the number sign, B is the number of the input from which counting of positions starts for sending the integer part of the real number, and C is the number of the input from which counting of positions starts for sending the fractional part of the real number. This type has the size of 1+16+16 = 33 positions, and uses the same number of inputs. With that, the rule for choosing the input for B and C part is the same as the one for the integer data type.
* Big integer. Specified as *big_integer_%name%* = A,B, where *%name%* is the cell name, and A is the number of the input where counting of positions starts for sending the first part of the number, B is the number of the input where counting of positions starts for sending the second part of the number. This type has the size of 16+16 = 32 positions, and uses the same number of inputs. With that, the rule for choosing the input for A and B part is the same as the one for the integer data type. The main purpose of this type is sending big non-negative integers.

To set the configuration for digital outputs of the robot - *DO*, the same data types are applied, the rules of their description being the same. The only difference is that digital outputs are configured in the section named *DO*.

The *FANUC* Module supports invoking *PNS* programs in the robot, but this requires allocating additional inputs/outputs with the following names.

For *DI*:

* *logic_cycle_stop*
* *logic_fault_reset*
* *logic_start*
* *logic_enable*
* *logic_input_PNS1*
* *logic_input_PNS2*
* *logic_input_PNS3*
* *logic_input_PNS4*
* *logic_input_PNS5*
* *logic_input_PNS6*
* *logic_input_PNS7*
* *logic_input_PNS8*
* *logic_PNS_strobe*
* *logic_prod_start*

For *DO*:

* *logic_cmdenabled*
* *logic_prg_running*
* *logic_prg_paused*
* *logic_motion_held*
* *logic_fault*
* *logic_busy*
* *logic_out_PNS1*
* *logic_out_PNS2*
* *logic_out_PNS3*
* *logic_out_PNS4*
* *logic_out_PNS5*
* *logic_out_PNS6*
* *logic_out_PNS7*
* *logic_out_PNS8*
* *logic_snack*

In setting all these inputs/outputs, the module will have access to functions *run_program* and *run_program_soft* that use the *PNS* mechanism.

The available functions of the module are shown in the Table:

|Definition|Description|
|:------------- |:------------------|
|run_program(number)|Invokes the *PNS* program at number *num* in the robot, and awaits its completion. After normal termination returns default value, 0.0, and in case of an error, an exception is thrown. This function is available only if you have made configuration of inputs / outputs that are required for operation of the PNS mechanism. If no configuring was made, the function throws an exception.|
|run_program_soft(num)|Invokes the *PNS* program at number *num* in the robot, and does not wait for its completion. The running program may only be terminated manually through operating corresponding inputs/outputs that are responsible for controlling the *PNS* mechanism. This function is available only if you have made configuration of inputs / outputs that are required for operation of the PNS mechanism. If no configuring was made, the function throws an exception.|
|set_logic_di(name, value)|Setting the value to value for a Boolean input named *name*. The function throws an exception if the input with the specified name has not been found. A positive signal is sent to the input, if the *value* of value is not zero, otherwise, a negative signal is sent.|
|get_logic_di(name)|Obtaining the value of the Boolean input named *name*. The function throws an exception if the input with the specified name has not been found. The function returns 1 if the signal at the input is positive, and 0 if it is negative.|
|get_logic_do(name)|Obtaining the value of the Boolean output named *name*. The function throws an exception if the input with the specified name has not been found. The function returns 1 if the signal at the input is positive, and 0 if it is negative.|
|set_integer_di(name, value)|Setting the value to value for an integer input named *name*. The function throws an exception if the input with the specified name has not been found. If you are setting the *value* of value as real, it is rounded according to the mathematical rules.|
|get_integer_di(name)|Obtaining the value of the integer input named *name*. The function throws an exception if the input with the specified name has not been found.|
|get_integer_do(name)|Obtaining the value of the integer output named *name*. The function throws an exception if the input with the specified name has not been found.|
|set_real_di(name, value)|Setting the value to *value* for an integer input named *name*. The function throws an exception if the input with the specified name has not been found. In the sent value *value*, the digits after the 3rd decimal position will be dropped.|
|get_real_di(name)|Obtaining the value of the real input named *name*. The function throws an exception if the input with the specified name has not been found.|
|get_real_do(name)|Obtaining the value of the real output named *name*. The function throws an exception if the input with the specified name has not been found.|
|set_big_integer_di(name, value)|Setting the *value* to value for a big digit input named *name*. The function throws an exception if the input with the specified name has not been found. If you set the value of value as real, it is rounded according to the mathematical rules.|
|get_big_integer_di(name)|Obtaining the value of the big integer input named *name*. The function throws an exception if the input with the specified name has not been found.|
|get_big_integer_do(name)|Obtaining the value of the big integer output named *name*. The function throws an exception if the input with the specified name has not been found.|

The module of the *FANUC* robot has no axes available for the manual control axis.

### 17.2 An example of connecting function modules

### 17.2.1 Quick connection of the math module to RCML

The *math* functional module is used to access mathematical functions similar to those in the math.h library in *C++*.

1. To include the *math* module, add an entry to section *[function_modules]* in the RCML configuration file in accordance with Section ["Installation and Configuration of RCML Compiler and Interpreter"](http://rcml.info/eng/aboutrcml#paragraph-146). The module does not require additional configuration.
2. Now, there are the following functions at your disposal:

|Definition |Description||An exception is thrown, if|
|:------------- |:------------------|:------|:----|
|pow(a, b)|Powering a to power b.|||
|sqrt(a)|Returns square root of a; a cannot be less than 0.||a < 0|
|abs(a)|Returns the absolute value of a.|||
|rand(a,b)|Returns a random integer in the range [b; a+b]; a cannot be less than or equal to 0.||a ≤ 0|
|sin(a)|Functions sin, cos, tan accept values in radians.|Returns the sine of a.||
|cos(a)|Functions sin, cos, tan accept values in radians.|Returns the cosine of a.||
|tan(a)|Functions sin, cos, tan accept values in radians.|Returns the tangent of a.||
|asin(a)|Returns the arcsine of a. Because of the function properties, argument a must be within [-1; 1].|Functions asin, acos, atan return values in radians.|a ∉ [-1;1]|
|acos(a)|Returns the arc cosine of a. Because of the function properties, argument a must be within [-1; 1].|Functions asin, acos, atan return values in radians.|a ∉ [-1;1]|
|atan(a)|Returns the arc tangent of a.|Functions asin, acos, atan return values in radians.||
|exp(a)|Returns e to power a.|||
|log(a)|Returns the natural logarithm of a. Because of the function properties, argument a must be more than 0.||a ≤ 0|
|log10(a)|Returns the decimal logarithm of a. Because of the function properties, argument a must be more than 0.||a ≤ 0|

1. In accordance with Section ["Calling Functions"](http://rcml.info/eng/aboutrcml#paragraph-164), the syntax of the program using the math module is as follows:
```
 function main() {
	r = 9;
	r = math.sqrt(r);
	system.echo(r);
}
```
The result of running this program is number 3.0000

1. The following example shows throwing an exception in case of incorrect input data.
```
function main() {
	try {
		r=math.log(0);
	} catch {
		system.echo("Throw exception log(0) undefined");
	}
}
```
More detail about using exceptions is available in section ["Exceptions"](http://rcml.info/eng/aboutrcml#paragraph-163).

### 17.3 An example of connecting control modules

### 17.3.1 Quick connection of control test module to RCML

The *test* control module is used to imitate obtaining axis values from the control devices.

To enable the *test* module, you need to add the property *module* and set its value to *"test"* in section *[control_modules]* in the configuration RCML file according to Section ["Installation and Configuration of RCML Compiler and Interpreter"](http://rcml.info/eng/aboutrcml#paragraph-146). The test control module does not require additional configuration.

The following is an example of using a test control module in interaction with test robot:
```
function main() {
	@lr = robot_test; 
	system.hand_control(@lr,"test","X","X");
}
```
In running this program for *10* seconds, a random value is sent along the *X*-axis once per second. For functioning of the program, the test robot module should be connected. More detail about connecting is available in section "Quick connection of robot test module to RCML".

The test control module has the following axes available:


|Name |The upper limit|The lower limit|Note|
|:------------- |:------------------:|:------:|:----|
|X|100|-100||
|Y|1|0|	Binary|
|Z|100|0|||

The values of axes that are transmitted every second:

|Name|1|2|3|4|5|6|7|8|9|10| 
|:-------- |:-------:|:--------:|:-----:|:--------:|:-------:|:--------:|:-----:|:-------:|:--------:|:-----:|
|X|100|-30.1|-2.58|48.9|99.01|-100|12.0|-36.9|0.25|0|
|Y|0|0|0|1|1|0.5|0.25|0.1|0.75|0.35|
|Z|4.56|0|78.9|100|50|48.8|66.7|32.4|40|20|

### 17.3.2 Quick connection of the keyboard module to RCML

![](http://rcml.info/images/aboutrcml/16_3_2.jpg)

The *keyboard* control module is used to manually control the robot with the use of a keyboard.

1. To activate a *keyboard* module, add entry "property *module"* to section *[control_modules]* to the RCML configuration file, and set it equal to *keyboard* in accordance with section ["Installation and Configuration of RCML Compiler and Interpreter"](http://rcml.info/eng/aboutrcml#paragraph-146).
2. In the folder of the *keyboard* module in the *control_modules* directory, create the *config.ini* module configuration file. 
3. The configuration file of the *keyboard* module describes which axes are available to the programmer for interaction with the robot in the manual mode. To add a new axis, adhere to the following rules of describing it:

3.1. In case of adding a new axis, add axis name property to section *[mapped_axis]* and set it equal to the value of the keyboard key in *HEX* format; there may be several keyboard button values for one axis. In general, an entry in the *[mapped_axis]* section will look as follows:

*axis_name = keyboard_button_value_in_HEX_format*

3.2. You should set the maximum and the minimum values that the axis may take. To do so, add to the *config.ini* configuration file a section named as the name of the axis, and set the *upper_value* and *lower_value* properties for passing the values of the axis' maximum and minimum. In general, the section looks as follows:

*[axis_name]*

*upper_value = the_max_axis_value*

*lower_value = the_min_axis_value*

3.3. Next, you should determine what value the axis will have after a previously defined button on the keyboard is pressed. The values are defined by creating a section with the name consisting of the axis name and the value of keyboard button in *Hex* format, separated by underscores. To set the default (not pressed) and pressed state values, *unpressed_value* and *pressed_value* are used, where the values are passed. In general, the section in this case will look as follows: 

*[axis_name_keyboard_button_value_in_HEX_format]*

*pressed_value = axis_value_with_pressed_button*

*unpressed_value = axis_value_with_released_button *

For clarity, here is an example of configuration for the *keyboard* module with comments
```
;Section for Linux
[options]
input_path = /dev/input/event2; Path to the input stream file

;Mandatory section
[mapped_axis]
;axis_name = key_code (in HEX format)
go = 0x26; up_arrow
go = 0x28;down_arrow

;Description of the go axis, should always have both keys
[go]
upper_value = 1 ;Upper limit of axis values
lower_value = -1 ;Lower limit of axis values

;Description of the go axis behavior for the *up_arrow* key (0x26)
[go_0x26]
pressed_value = 1 ;If *up_arrow* is pressed, the axis value to be set to 1
unpressed_value = 0 ;If *up_arrow* is released, the axis value to be set to 0

;Description of the go axis behavior for the *down_arrow* key (0x28)
[go_0x28]
pressed_value = -1 ;If *down_arrow* is pressed, the axis value to be set to 1
unpressed_value = 0 ;If *down_arrow* is released, the axis value to be set to 0
```

An example of a module configuration description includes one *go* axle, which will return the maximum value of 1 when up arrow is pressed, and 0 when it is released, or -1 when the down arrow is pressed, and 0 when it is released. More detailed information about the axes and manual control is available in section ["General Information"](http://rcml.info/eng/aboutrcml#paragraph-173).

As an example, an advanced configuration file of the *keyboard* module is provided, where the following axes are initialized:

* *[go]* – up and down arrows are used as control buttons;
* *[rotate]* – the axis is controlled by left and right arrows;
* *[locked]* – used to lock the robot by pressing the "A" button on the keyboard.

The following is a listing of the configuration file of the *keyboard* module with three axes:
```
[mapped_axis]
go = 0x26
go = 0x28
rotate = 0x25
rotate = 0x27
locked = 0x41

[go]
upper_value = 1
lower_value = -1

[rotate]
upper_value = 1
lower_value = -1

[locked]
upper_value = 1
lower_value = 0

[locked_0x41]
pressed_value = 1
unpressed_value = 0

[go_0x26]
pressed_value = 1
unpressed_value = 0
	
[go_0x28]
pressed_value = -1
unpressed_value = 0

[rotate_0x25]
pressed_value = 1
unpressed_value = 0

[rotate_0x27]
pressed_value = -1
unpressed_value = 0
```
The following is an example text of a program in the RCML. The program invokes the test robot, which had been connected according to section ["Quick start"](http://rcml.info/eng/aboutrcml#paragraph-144) and looks as follows:
```
function main() {
	@tr = robot_test; 
	system.hand_control(@tr,"keyboard","X","go");
}
```
The first line of code creates a variable that is attached to the test robot; information about what a test robot is, and how to connect to it is available in Section "Quick connection of robot test module to RCML".

The second line switches the test robot to manual control mode. As previously described, the *go* axis takes its value depending on pressed up and down arrows, and has the maximum value 1 and the minimum value -1, which translates their X-axis of the robot. However, during the program execution, the value of the axis of the control device will not be equal to the value of the axis of the robot. The axis is automatically scaled, i.e., the maximum value of the key is converted into the maximum axis value of the robot. A similar situation is observed when the down arrow button is pressed. The *keyboard* control module gets the minimum value of the keypad axis, and converts it to the minimum value of the robot axis. Thus, when the up arrow is pressed, the test robot will announce that it will move to the maximum position 100, and when the down arrow is pressed, it will move to -100.

The test robot has two extra axes - *Y, Z*. Using the configuration file of the *keyboard* module described in [step 3](http://rcml.info/eng/aboutrcml#step_3), let us add one more robot control axis. Let there be the *"rotate"* axis value passed along the existing values to the Z-axis of the test robot. The program code in this case will be as follows:
```
function main() {
	@tr = robot_test; 
	system.hand_control(@tr,"keyboard","X","go","Y","rotate");
}
```
During the program execution, pressing the up or down arrows will call a test message from the robot, stating that a command has been received to change the values on the *X*-axis and *Y*-axis, when the left or right buttons are pressed. With that, the values received by the robot will be the maximum for these axes.

More information about manual control is available in Section ["Hand Control Mode"](http://rcml.info/eng/aboutrcml#paragraph-53).

### 17.3.3 Quick connection of gamepad module to RCML

![](http://rcml.info/images/aboutrcml/16_3_3.jpg)

The *gamepad* control module ensures manual robot control with the use of a gamepad.

The number of the used axes depends on the contents of the *config.ini* module configuration file. The maximum number of axes that can be defined in the configuration file is *18*, including:

* 11 binary axes corresponding to buttons;
* 4 axes for the sticks;
* 2 axes for the D-pad;
* 1 axis named *"Exit"* assigns the manual completion function to a button.

1. To activate a *gamepad* module, add entry "property *module"* to section *[control_modules]* to the RCML configuration file, and set it equal to *«gamepad»* in accordance with Section ["Installation and Configuration of RCML Compiler and Interpreter"](http://rcml.info/eng/aboutrcml#paragraph-146).
2. Next, go into the RCML modules folder where control modules (*control_modules*) are located, then go to the *«gamepad»* folder.
3. Create an empty *config.ini* file in the *«gamepad»* folder. Later, this file will be used for joystick configuration.
4. Next, configure the config.ini configuration file of the module. It is proposed to use one of the two ready module configurations that are suitable for most gamepads:

4.1. The universal module configuration file for interaction with gamepad No. 1
```
[axis]
Exit = 9
B1 = 1
B2 = 2
B3 = 3
B4 = 4
L1 = 7
L2 = 5
R1 = 8
R2 = 6
start = 10
T1 = 11
T2 = 12
RTUD = 13
RTLR = 16
LTUD = 15
LTLR = 14
arrowsUD = 17
arrowsLR = 18

[B1]
upper_value = 1
lower_value = 0
	
[B2]
upper_value = 1
lower_value = 0
	
[B3]
upper_value = 1
lower_value = 0
	
[B4]
upper_value = 1
lower_value = 0
	
[L1]
upper_value = 1
lower_value = 0
	
[L2]
upper_value = 1
lower_value = 0
	
[R1]
upper_value = 1
lower_value = 0
	
[R2]
upper_value = 1
lower_value = 0
	
[start]
upper_value = 1
lower_value = 0
	
[T1]
upper_value = 1
lower_value = 0
	
[T2]
upper_value = 1
lower_value = 0
	
[RTUD]
upper_value = 0
lower_value = 65535
	
[RTLR]
upper_value = 0
lower_value = 65535
	
[LTUD]
upper_value = 0
lower_value = 65535

[LTLR]
upper_value = 0
lower_value = 65535
	
[arrowsUD]
upper_value = 1
lower_value = -1
	
[arrowsLR]
upper_value = 1
lower_value = -1
```
4.2. The universal module configuration file for interaction with gamepad No. 2:
```
[axis]
Exit = 9
B1 = 4
B2 = 2
B3 = 1
B4 = 3
L1 = 5
L2 = 7
R1 = 6
R2 = 8
start = 10
T1 = 11
T2 = 12
RTUD = 16
RTLR = 13
LTUD = 15
LTLR = 14
arrowsUD = 17
arrowsLR = 18
	
[B1]
upper_value = 1
lower_value = 0
	
[B2]
upper_value = 1
lower_value = 0
	
[B3]
upper_value = 1
lower_value = 0
	
[B4]
upper_value = 1
lower_value = 0

[L1]
upper_value = 1
lower_value = 0

[L2]
upper_value = 1
lower_value = 0

[R1]
upper_value = 1
lower_value = 0
	
[R2]
upper_value = 1
lower_value = 0
	
[start]
upper_value = 1
lower_value = 0

[T1]
upper_value = 1
lower_value = 0

[T2]
upper_value = 1
lower_value = 0

[RTUD]
upper_value = 0
lower_value = 65535

[RTLR]
upper_value = 0
lower_value = 65535
	
[LTUD]
upper_value = 0
lower_value = 65535

[LTLR]
upper_value = 0
lower_value = 65535

[arrowsUD]
upper_value = 1
lower_value = -1
	
[stick_zero_positions]
axis_1 = 31487
axis_2 = 31487
axis_3 = 31487
axis_4 = 31487
arrows = 4294967295

[options]
input_path = /dev/input/js1

[arrowsLR]
upper_value = 1
lower_value = -1
```
If ready module configuration files are used, steps *5* through *8* should be skipped. If the configuration files do not provide the desired level of control, it is necessary to additionally perform the skipped steps.

**Before** proceeding to controlling a real physical robot, it is recommended to validate the configuration of «gamepad» on a test robot, the methods of connecting the test robot to RCML is available in Section "Quick connection of robot test module to RCML".

5. Make sure the gamepad is connected to the computer. Otherwise, when the *'calibrator.exe'* is executed further, a *‘cant CreateDevice’* error will be generated.

6. To fill the configuration file, run the *'calibrator.exe'* file.
This console application will start the calibration wizard. The instructions of this wizard are to be followed to fill the *config.ini* file.
To skip assigning a button, it is enough to wait for the time specified as the parameter. (5 seconds by default)

7. If pressing a button was skipped, or a wrong button has been pressed, close the window, restart *'calibrator.exe'*, and follow the instructions more carefully.

8. Thus, the *gamepad* module configuration will be created. If this file is opened, it will contain the names of the axes and their threshold values.

9. The following is the table with transcript of each available axis of the gamepad:

|Name |The upper limit|The lower limit|Note|
|:------------- |:------------------:|:------:|:----|
|B1|1|0|Binary axis. Corresponds to any button from the ABXY group of buttons of the x-box gamepad.|
|B2|1|0|Binary axis. Corresponds to any button from the ABXY group of buttons of the x-box gamepad.|
|B3|1|0|Binary axis. Corresponds to any button from the ABXY group of buttons of the x-box gamepad.|
|B4|1|0|Binary axis. Corresponds to any button from the ABXY group of buttons of the x-box gamepad.|
|L1|1|0|Binary axis. Corresponds to the button with the same name of the x-box gamepad|
|L2|1|0|Binary axis. Corresponds to the button with the same name of the x-box gamepad|
|R1|1|0|Binary axis. Corresponds to the button with the same name of the x-box gamepad|
|R2|1|0|Binary axis. Corresponds to the button with the same name of the x-box gamepad|
|start|1|0|Binary axis. Corresponds to the button with the same name of the x-box gamepad|
|T1|1|0|Binary axis. Corresponds to the button with the same name of the x-box gamepad|
|T2|1|0|Binary axis. Corresponds to the button with the same name of the x-box gamepad|
|RTUD|Assigned|Assigned|Discrete axis. Corresponding to the right stick.|
|RTLR|Assigned|Assigned|Discrete axis. Corresponding to the right stick.|
|LTUD|Assigned|Assigned|Discrete axis. Corresponds to the left stick.|
|LTLR|Assigned|Assigned|Discrete axis. Corresponds to the left stick.|
|arrowsUD|1|-1|Discrete axis. Corresponds to the D-pad of the gamepad.|
|arrowsLR|1|-1|Discrete axis. Corresponds to the D-pad of the gamepad.|

The following is an example text of a program in the RCML language. The program invokes the test robot, which had been connected according to Section ["Quick start"](http://rcml.info/eng/aboutrcml#paragraph-144) and looks as follows:
```
function main() {
	@tr = robot_test; 
	system.hand_control(@tr,"gamepad","X","RTUD");
}
```
The first line of code creates a variable that the test robot is assigned to.

The second line switches the test robot to manual control mode from the gamepad.

During the program execution, using the right stick in the up-down direction will pass value of the *X*-axis to the test robot. Each time the position of the stick changes, the window will display a message stating that the test robot has received a change of the value along the *X*-axis.

More information about manual control is available in Section ["Hand Control Mode"](http://rcml.info/eng/aboutrcml#paragraph-53).

### 17.3.4 Quick connection of Myo to RCML

![](http://rcml.info/images/aboutrcml/16_3_4_1.png)

*Myo* is an armband that allows using various gestures to control a PC, a smartphone or a tablet.

More information is available from the official website of the device:

![](http://rcml.info/images/aboutrcml/16_3_4_2.png)

The *myo* control module is used for manual control of the robot using the *Myo* device, which detects gestures and position of the hand in space.

1. First, you have to run "install and configure *Myo*" on your computer, as shown in [Setting up the Myo armband with your Windows PC](https://support.getmyo.com/hc/en-us/articles/202657596-Setting-up-the-Myo-armband-with-your-Windows-PC).

2. Next, configure the following settings for *Myo Connect*.

![](http://rcml.info/images/aboutrcml/29615c46fafd1f236f7eaf933c52927c.png)

2.1. Configure *General* settings in accordance with the Figure.

![](http://rcml.info/images/aboutrcml/187895f5103d5ec02f440ceafd0feb95.png)

2.2. Configure *Presentation* Mode as shown in the Figure.

![](http://rcml.info/images/aboutrcml/f55e18617d2fd82ebae7348ea133e684.png)

2.3. Moreover, it is necessary to disconnect *Connector* and *Presentation Mode* in accordance with Figure.

![](http://rcml.info/images/aboutrcml/e644be3829dacdeb1336e4b8ebcf87f5.png)

2.4. You should also configure *Myo Keyboard Mapper* as shown in the Figure.

* To activate a *myo* module, add entry "property module" to section *[control_modules]* to the RCML configuration file, and set it equal to *myo* in accordance with Section ["Installation and Configuration of RCML Compiler and Interpreter"](http://rcml.info/eng/aboutrcml#paragraph-146).

The module does not require additional configuration, and will by default connect to the first armband in the system.
```
function main() {
	@r = robot_test;
	system.hand_control(@r,"myo","X","fist");
}
```
The first line of the program creates a variable that is attached to the test robot; information about how to connect the test robot is available in Section "Quick connection of robot test module to RCML".

The second line of the program specifies that the robots will be operated in manual mode. That is, the value of the *fist* axis of the Myo control device will be passed to the *X*-axis of the *@r* test robot. More information about what manual control function means is available in Section ["Hand Control Mode"](http://rcml.info/eng/aboutrcml#paragraph-53).

During the program execution, make the *"fist"* gesture (double your fist), the gesture will be recognized by *Myo* and via the *myo* control module, and the new value of the *X*-axis will be passed to the test robot. Since 1 is the maximum value of the *"fist"* axis for *Myo*, and for the test robot the maximum value of the *X*-axis is 100, the upper limit of the *fist* axis value will be converted to the upper limit of the *X*-axis. Thus, the robot will change the value of the *X*-axis to the maximum possible value – 100.

The *Myo* armband can recognize the following gestures:

* fist;
* fingers spread;
* double tap;
* wave out;
* wave in;
* rest (no gesture, relaxed hand).

All these gestures are available to the programmer by default.

In addition, the following axes in the module are available for the programmer:

|Name |The upper limit|The lower limit|Note|
|:------------- |:------------------:|:------:|:----|
|locked|1|0|Binary axis. Shows the status of the Myo bracelet: blocked - 1, unblocked - 0.|
|fist|1|0|Binary axis. If the fist gesture is observed, 1, otherwise - 0.|
|left_or_right|1|-1|Discrete axis. Convenient for specifying the direction of movement or rotation. It is based on the wave out and wave in gestures. Automatically determines rightward or leftward hand direction in these gestures (no matter which arm the armband is worn on) and takes the following values: -1 for leftward, 0 - no gesture, and 1 for rightward.|
|fingers_spread|1|0|Binary axis. 1 if the "fingers spread" gesture is detected, otherwise - 0.|
|double_tap|1|0|Binary axis. 1 if the "double tap" gesture is detected, otherwise - 0.|
|fist_pitch_angle|18|0|Continuous axis. Detects the pitch of the armband relative to the earth, in case of the "fist" gesture.|
|fist_roll_angle|18|0|Continuous axis. Detects the roll of the armband relative to the earth, in case of the "fist" gesture.|
|fist_yaw_angle|18|0|Continuous axis. Detects the yaw of the armband relative to the earth, in case of the "fist" gesture.|
|fingers_spread_pitch_angle|18|0|Continuous axis. Detects the pitch of the armband relative to the earth, in case of the "fingers spread" gesture.|
|fingers_spread_roll_angle|18|0|Continuous axis. Detects the roll of the armband relative to the earth, in case of the "fingers spread" gesture.|
|fingers_spread_yaw_angle|18|0|Continuous axis. Detects the yaw of the armband relative to the earth, in case of the "fingers spread" gesture.|

Notes to the table:

1. **The values of all axes are transmitted 20 times per second**.
2. The values of the axes with suffix *"angle"* are changed and transmitted only after detecting the corresponding gesture.
3. If the armband is blocked, the values of all gestures axes, except for the axis *"locked"* and the axes with suffix *"angle"*, will be equal to 0.
4. The armband is blocked automatically if for some short time (1-2 seconds) the sensors detect an unknown gesture, or the *"rest"* gesture is detected, as well as in case shift of the armband on the arm is detected, or if the armband is taken off the arm.
5. The armband in unblocked by the *"double tap"* gesture, same as in many *Myo*-compatible applications.
6. It is recommended to use caution when using simultaneously the *"fist"* or *"fingers_spread"* axes, and the axes with names containing the same *"fist"* or *"fingers_spread"* prefixes, respectively, for controlling the robot. The reason is that the axes of gestures take value 1 when there is an attempt to change the values of axes that are active after the corresponding gesture, since it requires detection of this gesture.
Beside the test robot, another RCML-compatible robot may be connected to the RCML system.

### 17.4 An example of connecting choice robot modules

### 17.4.1 Quick connection of choosing avg module

The robot selection module *avg* implements the simplest logics for choosing the fastest robot based on the *RCML* statistical data.

First, the module starts populating the statistical data into the database so that each robot from the set of available robots performs the required function at least once.

After that, it will always choose the robot with the shortest average time of performing the required function by its (robot's) number of attempts to perform this function.

Enabling the *avg* module is standard: add the *module* property to section *[choice_modules]* of the *RCML* configuration file, and set it equal to avg in accordance with section ["Installation and Configuration of RCML Compiler and Interpreter"](http://rcml.info/eng/aboutrcml#paragraph-146) of the *RCML* Manual.

No additional configuration of the avg module is required. However, *RCML* should be in the mode of writing statistical information, otherwise the module will prohibit invocation.

