---
title:  "RealtimeLogger - An NLog Companion for Real-Time Logging"
excerpt: "A C# class that works in conjunction with the NLog 'MethodCall' logging target to expose log messages via an event in real time."
header:
  teaser: "realtimelogger-teaser.PNG"
categories: "Tools"
tags: "Intermediate"
---

A few months ago I found myself wanting to be able to display messages logged by NLog in an application, specifically on a Windows Form
first, and later via a SignalR hub in a web application.  I couldn't find any suitable events within NLog, however I did discover the [MethodCall](https://github.com/NLog/NLog/wiki/MethodCall-target)
logging target.

This target allows you to specify a static method to be invoked whenever a message is logged to a logger configured to write to it.  While this is pretty straightforward, I found the static method a little
difficult to integrate into my forms application, and I wanted the ability to retrieve logs that were captured prior to my SignalR hub being started so that I could load them later.

To answer both of these needs I created the RealtimeLogger class.

# Design

The design of the RealtimeLogger class is extremely simple, containing only three primary elements.  

The first element is a public static method named ```AppendLog```.  This method accepts five string parameters representing various details about log messages and uses them to create a new instance of 
```RealtimeLoggerEventArgs```, which parses the strings into the target data types for each field.  ```AppendLog``` then invokes the ```LogAppended``` event and passes the new ```RealtimeLoggerEventArgs``` to it.

The second element is the ```LogAppended``` event.  This event is raised each time the ```AppendLog``` method is invoked by NLog.  Anything in the application that needs to be informed of new logs simply needs to subscribe
to this event and parse the supplied ```RealtimeLoggerEventArgs``` to retrieve the log details.

The third element is a queue of Type ```Queue<RealtimeLoggerEventArgs>```.  The queue contains the last N ```RealtimeLoggerEventArgs``` instances, where N is user-configurable by specifying a variable in the NLog configuration
with the name ```RealtimeLogger.LogHistoryLimit```.  The nature of a queue is such that when new items are added, if it has reached it's limit, the oldest items are discarded.  The queue is exposed as the static property 
```RealtimeLogger.LogHistory```.

# Installation

Install from the NuGet gallery GUI or with the Package Manager Console using the following command:

```Install-Package NLog.RealtimeLogger```

# Usage

## NLog Configuration

Note that the following instructions are applicable if you aren't using the NuGet package (e.g. you compiled the code yourself, or inserted the source in your project).

Copy the RealtimeLogger.cs file into your project and rename the namespace to fit your application.  In the example below we'll pretend we named it ```MyNamespace```.

In your NLog configuration, add the following target:

```xml
<target name="method" xsi:type="MethodCall" className="MyNamespace.RealtimeLogger, MyNamespace" methodName="AppendLog">
  <parameter layout="${threadid}"/>
  <parameter layout="${longdate}"/>
  <parameter layout="${level}"/>
  <parameter layout="${logger}"/>
  <parameter layout="${message} ${exception:format=tostring}"/>
</target>
```

Note that in the ```className``` attribute, the fully qualified name of the ```RealtimeLogger``` class is used.  This includes the namespace, which in this case is ```MyNamespace```.  The second part of the attribute must be set to the name of your assembly.  This will be defined in ```Properties\AssemblyInfo.cs``` within the ```AssemblyTitle``` attribute.

Next, add a logger for the method call to your NLog configuration:

```xml
<logger name="*" minlevel="Info" writeTo="method"/>
```

Change the ```minlevel``` attribute to whichever logging level you'd like to catch.

When you are finished, your NLog configuration should look something like this:

```xml
<nlog xmlns="http://www.nlog-project.org/schemas/NLog.xsd" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    autoReload="true" 
    throwExceptions="false" 
    internalLogLevel="Off" 
    internalLogFile="c:\temp\nlog-internal.log"
  >
  <targets>
    <target name="method" xsi:type="MethodCall" className="Example.RealtimeLogger, Example" methodName="AppendLog">
	  <parameter layout="${threadid}"/>
      <parameter layout="${longdate}"/>
      <parameter layout="${level}"/>
      <parameter layout="${logger}"/>
      <parameter layout="${message} ${exception:format=tostring}"/>
    </target>
  </targets>
  <rules>
    <logger name="*" minlevel="Info" writeTo="method"/>
  </rules>
</nlog>
```

#### NuGet Package Configuration

If you downloaded and installed the library from NuGet, your target should match the following:

```xml
  <targets>
    <target name="method" xsi:type="MethodCall" className="NLog.RealtimeLogger.RealtimeLogger, NLog.RealtimeLogger" methodName="AppendLog">
	  <parameter layout="${threadid}"/>
      <parameter layout="${longdate}"/>
      <parameter layout="${level}"/>
      <parameter layout="${logger}"/>
      <parameter layout="${message} ${exception:format=tostring}"/>
    </target>
  </targets>
```

## Event Handler

Next, create a method that can be bound to the RealtimeLogger's ```LogAppended``` event, like so:

```c#
private void AppendLog(object sender, RealtimeLoggerEventArgs e)
{
  Console.WriteLine("New log: " + e.Message);
}
```

Bind this event handler to the ```LogAppended``` event:

```c#
RealtimeLogger.LogAppended += AppendLog;
```

Generally you'll want to put the statement above in Main().  Note that any number of handlers can be added.

That's it!  Your handler should be invoked each time a new log is added to the logger.

## RealtimeLoggerEventArgs

The properties of the event arguments ```RealtimeLoggerEventArgs``` for the ```LogAppended``` event are as follows:

Property | Type | Description
--- | --- | ---
ThreadID | int | The ID of the tread from which the log message originated.
DateTime | DateTime | The DateTime corresponding to the instant the log was added to the logger.
Level | LogLevel | The logging level used to add the log to the logger.
Logger | string | The name of the logger.
Message | string | The log message.

Note that the constructor for ```RealtimeLoggerEventArgs``` parses the ```ThreadID```, ```DateTime``` and ```LogLevel``` passed from NLog from strings.  If parsing fails the code will prefix the log message with an error before adding it to the log queue.

If the specified ```ThreadID``` value from NLog fails to be parsed, the ThreadID property will be set to ```0``` and the following message will be prepended to the log message:

```[Invalid ThreadID; substituted with default]```

If the specified ```DateTime``` value from NLog fails to be parsed, the DateTime property will be set to ```DateTime.Now``` and the following message will be prepended to the log message:

```[Invalid DateTime; substituted with DateTime.Now]```

The DateTime property for the log message will also be set to DateTime.Now.

If the specified ```LogLevel``` fails to be parsed, the LogLevel property will be set to ```LogLevel.Info``` and the following message will be prepended to the log message:

```[Invalid LogLevel; substituted with LogLevel.Info]```

# Log Queue

A queue of log messages is provided and may be accessed using the ```LogHistory``` property of type ```Queue<RealtimeLoggerEventArgs>```.  Logs will accumulate in the queue until it reaches the size specified in the ```LogHistoryLimit``` property, after which point logs will be dequeued when new logs are enqueued.

The variable ```logHistoryLimit``` is used to initialize the value of the ```LogHistoryLimit``` property.  This can be changed by editing the class, or ```LogHistoryLimit``` can be changed programmatically at runtime.

# Notes

If the ```async``` option is used for the logging target the log event will not fire precisely in real time.  It would be a good idea to use the ```AsyncWrapper``` functionality described [here](https://github.com/nlog/NLog/wiki/AsyncWrapper-target) to limit the asynchronous execution to only those targets that need it, rather than adding the ```async``` attribute to the ```<targets>``` node of the config file.

## Configuration

Create and modify the NLog configuration variable "RealtimeLogger.LogHistoryLimit" to change the number of logs saved in the log history.  The default is 300.

```xml
  <variable name="RealtimeLogger.LogHistoryLimit" value="300"/>
```