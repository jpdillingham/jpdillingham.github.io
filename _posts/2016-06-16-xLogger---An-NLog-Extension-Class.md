---
title:  "xLogger - An NLog Extension Class"
excerpt: "A C# class that extends NLog.Logger and provides additional functionality for tracing the entry and exit, arbitrary checkpoints, exceptions and stack traces within methods."
header:
  teaser: "xlogger-teaser.PNG"
categories: "Tools"
tags: "Intermediate"
---

As I previously mentioned in my posts about [BigFont.cs](http://dillingham.ws/tools/BigFont-Update/), I've created a wrapper class for NLog that adds some additional functionality, primarily
around the tracing of the execution flow of a program.  I've named the wrapper xLogger; short for Extended Logger.

The primary purpose for this class is to make it easy to log the execution flow of a program.  The secondary purpose is to improve readability of log files with typographic elements such as 
larger fonts (pseudo), symbols and drawing elements.

The code can be found on GitHub [here](https://github.com/jpdillingham/xLogger).

# Installation

Install from the NuGet gallery GUI or with the Package Manager Console using the following command:

```Install-Package NLog.xLogger```

# Instantiating xLogger

Typically when using NLog you'll have a variable declaration at the top of each class like the following:

```c#
private static Logger logger = LogManager.GetCurrentClassLogger();
```

To create an instance of xLogger in the same fashion you'll need to change the type to ```xLogger```, you'll need to cast the result of ```GetCurrentClassLogger()``` to type ```xLogger```, and you'll 
need to pass the ```xLogger``` type to the ```GetCurrentClassLoger()``` factory method, like so:

```c#
private static xLogger logger = (xLogger)LogManager.GetCurrentClassLogger(typeof(xLogger));
```

If you prefer to explicitly name your loggers, substitute ```GetCurrentClassLogger()``` for ```GetLogger()``` and supply your long name in addition to the ```xLogger``` type in the method call, like so:

```c#
private static xLogger logger = (xLogger)LogManager.GetLogger("My logger", typeof(xLogger));
```

# Extension Methods

I've added the following public methods to support logging the execution flow of the program:

* EnterMethod(): Logs the entry of a method
* ExitMethod(): Logs the exit of a method
* Checkpoint(): Logs any arbitrary point within a method
* Exception(): Logs an exception
* StackTrace(): Logs a stack trace at any arbitrary point

Furthermore, I've added the following supportive/utility methods, primarily to improve readability:

* Multiline(): Logs string arrays and single strings containing linebreaks as multiple log messages
* MultilineWrapped(): Same as above, but wraps the messages in the styled header and footer
* Separator(): Logs a horizontal line
* Heading(): Uses BigFont.cs to print a large header from the supplied text
* SubHeading(): Uses BigFont to print a medium message from the supplied text
* SubSubHeading(): Uses BigFont to print a small message from the suuplied text

# Persistence

In addition to logging the flow of the program I wanted to be able to track the execution time of methods where it made sense.  To do this, I created a ```List<Tuple<Guid, DateTime>>``` called ```PersistedMethods``` to store a Guid
and timestamp for each method.  Items are added when the ```EnterMethod()``` method is invoked with the persistence option and are deleted within ```ExitMethod()```.

Entries in the ```PersistedMethods``` list can get "stuck" if the method that added the corresponding list item doesn't invoke ```ExitMethod()``` for whatever reason.  This could be because the programmer didn't add the call,
the method used a return statement prior to reaching the normal end, or an exception was thrown causing the method to terminate prematurely.  

To handle "stuck" items, the ```PrunePersistedMethods(int age)``` is provided.  This method can be called at any time and will delete list items with an age exceeding the provided ```age``` in seconds.  Furthermore, if the ```AutoPruneEnabled``` variable
is set to ```true``` (the default value), list items are pruned automatically on each call of ```ExitMethod()```.  The age of automatically pruned items is specified in ```AutoPruneAge``` (default value of 300 seconds).

## EnterMethod()

The ```EnterMethod()``` method is used to log the entry point of a method.  The method accepts an object array containing the method parameters and boolean used to determine whether to persist the timestamp of entry.  
The method returns a new ```Guid``` if persistence is used or, if persistence wasn't specified, ```default(Guid)``` is returned.

The static helper method ```xLogger.Params()``` is included to assist with the building of the object array.  Pass each method parameter, in order, to this method and an object array containing the passed arguments is returned.  
If you wish to exclude any arguments from logging, for example, if an object is very large or is of low value, replace the passed argument with ```new xLogger.ExcludedParam()```.  This maintains proper positioning, which is important so that reflection can retrieve the name and type of the parameter.

Note that the class ExampleObject in the example below has been created to demonstrate the serialization functionality and will continue to be used for the remainder of the examples.

### Example

```c#
public static void EnterMethodExample(int one, int two, ExampleObject three)
{
    logger.EnterMethod(xLogger.Params(one, two, three));

    // method body
    logger.Trace("Standard log message");
}
```

Notice how parameters one, two and three are all passed to the method using xLogger.Params().  If you wished to exclude parameter two, the correct syntax would be:

```c#
logger.EnterMethod(xLogger.Params(one, new xLogger.ExcludedParam(), three);
```

### Output

```
[x]: ┌─────────── ─ ───────────────────────── ─────────────────────────────────────────────────────────────────── ─────── ─    ─     ─ 
[x]: │ ──► Entering method: Void EnterMethodExample(Int32 one, Int32 two, ExampleObject three) (Program.cs:line 18) 
[x]: │   ├┄┈ one: 1 
[x]: │   ├┄┈ two: 2 
[x]: │   ├┄┈ three: { 
[x]: │   ├┄┈ three:   "num": 3, 
[x]: │   ├┄┈ three:   "str": "three", 
[x]: │   ├┄┈ three:   "list": [ 
[x]: │   ├┄┈ three:     1.1, 
[x]: │   ├┄┈ three:     2.2, 
[x]: │   ├┄┈ three:     3.3 
[x]: │   ├┄┈ three:   ] 
[x]: │   └┄┈ three: } 
[x]: └──────────────────── ───────────────────────────────  ─  ─          ─ ─ ─    ─   ─ 
[x]: Standard log message 
```

## ExitMethod()

The ```ExitMethod()``` method logs the exit point of a method.  The method accepts an object representing the method return value and an optional ```Guid```, used if the corresponding ```EnterMethod()``` was called with the persistence option.

### Example

```c#
public static ExampleObject ExitMethodPersistentExample(int one, int two)
{
    Guid persistedGuid = logger.EnterMethod(xLogger.Params(one, two), true);

    // method body

    logger.Trace("Standard log message");
    ExampleObject returnValue = new ExampleObject(1, "return", new double[] { 5.5 }.ToList());

    logger.ExitMethod(returnValue, persistedGuid);
    return returnValue;
}
```

### Output

```
[x]: ┌─────────── ─ ───────────────────────── ─────────────────────────────────────────────────────────────────── ─────── ─    ─     ─ 
[x]: │ ──► Entering method: ExampleObject ExitMethodPersistentExample(Int32 one, Int32 two) (Program.cs:line 28), persisting with Guid: 6cd26c76-69a6-4425-a316-bf03368108b0 
[x]: │   ├┄┈ one: 1 
[x]: │   └┄┈ two: 2 
[x]: └──────────────────── ───────────────────────────────  ─  ─          ─ ─ ─    ─   ─ 
[x]: Standard log message 
[x]: ┌─────────── ─ ───────────────────────── ─────────────────────────────────────────────────────────────────── ─────── ─    ─     ─ 
[x]: │ ◄── Exiting method: ExampleObject ExitMethodPersistentExample(Int32 one, Int32 two) (Program.cs:line 35), Guid: 6cd26c76-69a6-4425-a316-bf03368108b0 
[x]: │   ├┄┈ return: { 
[x]: │   ├┄┈ return:   "num": 1, 
[x]: │   ├┄┈ return:   "str": "return", 
[x]: │   ├┄┈ return:   "list": [ 
[x]: │   ├┄┈ return:     5.5 
[x]: │   ├┄┈ return:   ] 
[x]: │   └┄┈ return: } 
[x]: ├──────────────────────── ─       ──  ─ 
[x]: │ ◊ Method execution duration: 4.0107ms 
[x]: └──────────────────── ───────────────────────────────  ─  ─          ─ ─ ─    ─   ─ 

```

## Checkpoint()

The ```Checkpoint()``` method logs an arbitrary checkpoint anywhere within the method body.  The method accepts a string representing the name of the checkpoint, an object array containing an arbitrary list of variables, a string array containing the names of the variables contained in the object list, 
and an optional ```Guid```, used if the corresponding ```EnterMethod()``` call used the persistence option.

Any number of variables may be passed to the method.  Variables can be given names by passing an array of strings to the method.  These names will be used when printing the serialized value of each variable to the logger, or the names array can be omitted and variables will be named ordinally.  If names are to be used, the number of items in the included string array must match the number of 
variables in the variable array.

### Example

```c#
public static int CheckpointExample(int one)
{
    Guid persistedGuid = logger.EnterMethod(xLogger.Params(one), true);

    logger.Trace("Standard log message");
    int two = 2;
    int three = 3;

    logger.Checkpoint("example checkpoint", xLogger.Vars(one, two, three), xLogger.Names("one", "two", "three"), persistedGuid);

    logger.Trace("Another standard log message");

    int returnValue = one + two + three;

    logger.ExitMethod(returnValue, persistedGuid);
    return returnValue;
}
```

### Output

```
[x]: ┌─────────── ─ ───────────────────────── ─────────────────────────────────────────────────────────────────── ─────── ─    ─     ─ 
[x]: │ ──► Entering method: Int32 CheckpointExample(Int32 one) (Program.cs:line 42), persisting with Guid: df75374b-9909-4680-84d5-1b0b5c52df09 
[x]: │   └┄┈ one: 1 
[x]: └──────────────────── ───────────────────────────────  ─  ─          ─ ─ ─    ─   ─ 
[x]: Standard log message 
[x]: ┌─────────── ─ ───────────────────────── ─────────────────────────────────────────────────────────────────── ─────── ─    ─     ─ 
[x]: │ √ Checkpoint 'example checkpoint' reached in method: Int32 CheckpointExample(Int32 one) (Program.cs:line 48), Guid: df75374b-9909-4680-84d5-1b0b5c52df09 
[x]: │   ├┄┈ one: 1 
[x]: │   ├┄┈ two: 2 
[x]: │   └┄┈ three: 3 
[x]: ├──────────────────────── ─       ──  ─ 
[x]: │ ◊ Current execution duration: 0.5015ms 
[x]: └──────────────────── ───────────────────────────────  ─  ─          ─ ─ ─    ─   ─ 
[x]: Another standard log message 
[x]: ┌─────────── ─ ───────────────────────── ─────────────────────────────────────────────────────────────────── ─────── ─    ─     ─ 
[x]: │ ◄── Exiting method: Int32 CheckpointExample(Int32 one) (Program.cs:line 54), Guid: df75374b-9909-4680-84d5-1b0b5c52df09 
[x]: │   └┄┈ return: 6 
[x]: ├──────────────────────── ─       ──  ─ 
[x]: │ ◊ Method execution duration: 1.0032ms 
[x]: └──────────────────── ───────────────────────────────  ─  ─          ─ ─ ─    ─   ─ 
```

## Exception()

The ```Exception()``` method logs exception details.  The method differs from the others in that it can log to levels other than Trace.  
The first parameter is of type ```LogLevel``` which is an enumeration of the logging levels within NLog.  Other parameters are the ```Exception``` that was caught and, as always, the optional ```Guid``` created if the method was entered with the persistence option.

### Example

```c#
public static void ExceptionExample()
{
    logger.EnterMethod();

    try
    {
        // intentionally raise an exception
        var arr = new string[5];
        Console.WriteLine(arr[5]);
    }
    catch (Exception ex)
    {
        logger.Exception(LogLevel.Error, ex);
    }
    finally
    {
        logger.ExitMethod();
    }
}
```

### Output

```
[x]: ┌─────────── ─ ───────────────────────── ─────────────────────────────────────────────────────────────────── ─────── ─    ─     ─ 
[x]: │ ──► Entering method: Void ExceptionExample() (Program.cs:line 63) 
[x]: └──────────────────── ───────────────────────────────  ─  ─          ─ ─ ─    ─   ─ 
[x]: ┌──┐┌─────────── ─ ───────────────────────── ─────────────────────────────────────────────────────────────────── ─────── ─    ─     ─ 
[x]: │██││ ╳ Exception 'IndexOutOfRangeException' caught in method: Void ExceptionExample() (Program.cs:line 73): 
[x]: │██││   └┄┈ "Index was outside the bounds of the array." 
[x]: │██│├──────────────────────── ─       ──  ─ 
[x]: │██││   └┄► Void Main(String[] args) 
[x]: │██││      └┄► Void ExceptionExample() 
[x]: │██│├──────────────────────── ─       ──  ─ 
[x]: │██││   ├┄┈ ex: { 
[x]: │██││   ├┄┈ ex:   "ClassName": "System.IndexOutOfRangeException", 
[x]: │██││   ├┄┈ ex:   "Message": "Index was outside the bounds of the array.", 
[x]: │██││   ├┄┈ ex:   "Data": null, 
[x]: │██││   ├┄┈ ex:   "InnerException": null, 
[x]: │██││   ├┄┈ ex:   "HelpURL": null, 
[x]: │██││   ├┄┈ ex:   "StackTraceString": " 
[x]: │██││   ├┄┈ ex:       at xLogger_Example.Program.ExceptionExample() in C:\Users\JP.WHATNET\OneDrive\Projects\xLogger\xLogger\xLogger-Example\xLogger-Example\Program.cs:line 69 
[x]: │██││   ├┄┈ ex:   " 
[x]: │██││   ├┄┈ ex:   "RemoteStackTraceString": null, 
[x]: │██││   ├┄┈ ex:   "RemoteStackIndex": 0, 
[x]: │██││   ├┄┈ ex:   "ExceptionMethod": "8\nExceptionExample\nxLogger-Example, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null\nxLogger_Example.Program\nVoid ExceptionExample()", 
[x]: │██││   ├┄┈ ex:   "HResult": -2146233080, 
[x]: │██││   ├┄┈ ex:   "Source": "xLogger-Example", 
[x]: │██││   ├┄┈ ex:   "WatsonBuckets": null 
[x]: │██││   └┄┈ ex: } 
[x]: └──┘└──────────────────── ───────────────────────────────  ─  ─          ─ ─ ─    ─   ─ 
[x]: ┌─────────── ─ ───────────────────────── ─────────────────────────────────────────────────────────────────── ─────── ─    ─     ─ 
[x]: │ ◄── Exiting method: Void ExceptionExample() (Program.cs:line 73) 
[x]: └──────────────────── ───────────────────────────────  ─  ─          ─ ─ ─    ─   ─ 
```

## Stack Trace
The ```StackTrace``` method logs the current call stack at the point of invocation.

### Example

```c#
public static void StackTraceExample()
{
    logger.StackTrace(LogLevel.Info);
}
```

### Output

```
[x]: ┌─────────── ─ ───────────────────────── ─────────────────────────────────────────────────────────────────── ─────── ─    ─     ─ 
[x]: │ @ Stack Trace from method: Void StackTraceExample() (Program.cs:line 83) 
[x]: │   └┄► Void Main(String[] args) 
[x]: │      └┄► Void StackTraceExample() 
[x]: └──────────────────── ───────────────────────────────  ─  ─          ─ ─ ─    ─   ─ 
```

## Additional Methods

* ```Multiline(LogLevel, string|string[])```: Logs the provided string or string array in multiple lines, split around the array elements or newline characters.
* ```MultilineWrapped(LogLevel, string|string[])```: Same as above, but wrapped in the stylized lines seen in the examples above.
* ```Separator(LogLevel)```: Logs a horizontal line
* ```Heading(LogLevel, string)```: Logs the provided string in large letters (provided by BigFont), followed by a separator.
* ```SubHeading(LogLevel, string)```: Logs the provided string in medium letters.
* ```SubSubHeading(LogLevel, string)```: Logs the provided string in smaller letters.

### Example

```c#
public static void OtherExamples()
{
    logger.Multiline(LogLevel.Trace, "hello \n world!");
    logger.MultilineWrapped(LogLevel.Trace, new string[] { "hello", "again", "world!!" });
    logger.Separator(LogLevel.Trace);
    logger.Heading(LogLevel.Trace, "Hello world!");
    logger.SubHeading(LogLevel.Trace, "Hello world!");
    logger.SubSubHeading(LogLevel.Trace, "Hello world!");
}
```

### Output

```
[x]: hello  
[x]:  world! 
[x]: ┌─────────── ─ ───────────────────────── ─────────────────────────────────────────────────────────────────── ─────── ─    ─     ─ 
[x]: │ hello 
[x]: │ again 
[x]: │ world!! 
[x]: └──────────────────── ───────────────────────────────  ─  ─          ─ ─ ─    ─   ─ 
[x]: ┌─────────── ─ ───────────────────────── ─────────────────────────────────────────────────────────────────── ─────── ─    ─     ─ 
[x]: │ ■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■ ■ ■■■■■■■■■■■■■■■ ■■  ■■ ■■   ■■■■ ■■     ■■     ■ ■ 
[x]: └──────────────────── ───────────────────────────────  ─  ─          ─ ─ ─    ─   ─ 
[x]: ┌─────────── ─ ───────────────────────── ─────────────────────────────────────────────────────────────────── ─────── ─    ─     ─ 
[x]: │ ███    ███ ████████ ███       ███        ▄██████▄         ███         ███  ▄██████▄  ████████▄   ███       ████████▄  ▄███▄  
[x]: │ ███    ███ ███      ███       ███       ███    ███        ███         ███ ███    ███ ███    ███  ███       ███   ▀███ █████  
[x]: │ ███    ███ ███      ███       ███       ███    ███        ███         ███ ███    ███ ███    ███  ███       ███    ███ █████  
[x]: │ ███▄▄▄▄███ ███▄▄▄   ███       ███       ███    ███        ███         ███ ███    ███ ███    ███  ███       ███    ███ █████  
[x]: │ ███▀▀▀▀███ ███▀▀▀   ███       ███       ███    ███        ███   ▄█▄   ███ ███    ███ ████████▀   ███       ███    ███ ▀███▀  
[x]: │ ███    ███ ███      ███       ███       ███    ███        ███  ▄█▀█▄  ███ ███    ███ ███▀██▄     ███       ███    ███  ███   
[x]: │ ███    ███ ███      ███       ███       ███    ███        ███ ▄█▀ ▀█▄ ███ ███    ███ ███  ▀██▄   ███       ███   ▄███        
[x]: │ ███    ███ ████████ █████████ █████████  ▀██████▀         █████▀   ▀█████  ▀██████▀  ███    ▀██▄ █████████ ████████▀   ███   
[x]: │ ■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■ ■ ■■■■■■■■■■■■■■■ ■■  ■■ ■■   ■■■■ ■■     ■■     ■ ■ 
[x]: └──────────────────── ───────────────────────────────  ─  ─          ─ ─ ─    ─   ─ 
[x]: ┌─────────── ─ ───────────────────────── ─────────────────────────────────────────────────────────────────── ─────── ─    ─     ─ 
[x]: │ ██   ██ ██████ ██       ██       ▄██████▄      ██      ██ ▄██████▄ ██████▄ ██       ██████▄  ▄███▄  
[x]: │ ██   ██ ██     ██       ██       ██    ██      ██      ██ ██    ██ ██   ██ ██       ██   ▀██ █████  
[x]: │ ██▄▄▄██ ██▄▄   ██       ██       ██    ██      ██      ██ ██    ██ ██   ██ ██       ██    ██ ▀███▀  
[x]: │ ██▀▀▀██ ██▀▀   ██       ██       ██    ██      ██ ▄██▄ ██ ██    ██ ██████▀ ██       ██    ██  ███   
[x]: │ ██   ██ ██     ██       ██       ██    ██      ██▄█▀▀█▄██ ██    ██ ██▀██▄  ██       ██   ▄██        
[x]: │ ██   ██ ██████ ████████ ████████ ▀██████▀      ███▀  ▀███ ▀██████▀ ██  ▀██ ████████ ██████▀   ███   
[x]: └──────────────────── ───────────────────────────────  ─  ─          ─ ─ ─    ─   ─ 
[x]: ┌─────────── ─ ───────────────────────── ─────────────────────────────────────────────────────────────────── ─────── ─    ─     ─ 
[x]: │ ██  ██ █████ ██     ██     ▄████▄    ██   ██ ▄████▄ ████▄ ██     █████▄ ▄██▄  
[x]: │ ██▄▄██ ██▄▄  ██     ██     ██  ██    ██   ██ ██  ██ ██ ██ ██     ██  ██ ▀██▀  
[x]: │ ██▀▀██ ██▀▀  ██     ██     ██  ██    ██ ▄ ██ ██  ██ ████▀ ██     ██  ██  ██   
[x]: │ ██  ██ █████ ██████ ██████ ▀████▀    ███▀███ ▀████▀ ██▀█▄ ██████ █████▀  ▄▄   
[x]: └──────────────────── ───────────────────────────────  ─  ─          ─ ─ ─    ─   ─ 
```

# Notes

## Performance

Performance is severely impacted with the Trace logging level enabled.  To alleviate this, configure NLog to asynchronously by adding the following to the configuration:

```<targets async="true">```

## Customization

Customize the output of the logger by editing the strings in the ```Variables``` region to your liking.
