---
hide:
  - navigation
#  - toc
---
# Creating Aneka Thread applications
## Introduction
Aneka thread applications are created using Aneka Thread API. 


## Requirements to be present on developer machine:

??? note "Vistual Studio 2022 Community Edition"
    1. You can use Visual Studio 2019 edition or further editions.
    2. You can download and set up using the __[guide]__.  
??? note "Aneka SDK"
    1. Aneka SDK can copied from the location where Aneka is installed. For example `C:\Program Files (x86)\Manjrasoft\Aneka.5.0\Tools\SDK`
    2. Thread, Comman and Runtime libraries are required to create a thread application.
??? note "Ankea configuration file `conf.xml`"
    1. Change the `SchedulerUri` value of IP address to your Master IP address.
    2. Change username and password to Master machine credentials.
    3. You can save the `conf.xml` in convenient location in developer machine.
    
    ``` xml
    <?xml version="1.0" encoding="utf-8" ?>
      <Aneka>
        <UseFileTransfer value="true" />
        <Workspace value="." />
        <SingleSubmission value="false" />
        <ResubmitMode value="AUTO" />
        <PollingTime value="1000" />
        <LogMessages value="true" />
        <SchedulerUri value="tcp://44.201.249.32:9090/Aneka" />
        <UserCredential type="Aneka.Security.UserCredentials" assembly="Aneka">
          <Instance username="admin" password="123456"/>
        </UserCredential>
      </Aneka>
    ```


[guide]: OtherTopics/visualstudio.md

## Creating your first "Hello World" Aneka Thread application:
Now let us create a first aneka thread application, that will print "Hellow World" on the console.

1. In developer machine, open the **Visual Studio 2022**
2. Click on `Create a new project` and click Next.
3. Choose `Console App(.NET Framework)` and Click Next.
4. Provide the following details
    1. Project name: `FirstThreadApplication`
    2. Framework: `.NET Framework 4.7.2`  
5. Application is created.
6. Now, let us add the references of Aneka libraries.
    1. Go to `Solution explorer` and right click on `References`--> `Add References`
    2. Go to Browse and look in to the location of SDK.
    3. Add the library `Aneka.Threading.dll`.
    4. Similarly add all the ddl in `Comman` and `Runtime` folders.
7. Import namespaces of aneka threads
    ```c#
    using Aneka;
    using Aneka.Threading;
    using Aneka.Entity;
    ```
8. Let us write a logic that aneka thread has to perform. Here `HeloWorld` class defines the logic with method `HelloPrint()`
    ```c#
    namespace SimpleThread
    {
    class Program
      {
        [Serializable]
        public class HelloWorld
        {
            public string result;
            public void HelloPrint()
            {
                result = "Hello Aneka...";
            }
        }
        static void Main(string[] args)
        {
        }
      }
    }
    ```
9. Now let us add the C# code by modifying the `Program.cs` to create a single aneka thread: 
    1. Move on to `Main` statement in `Program.cs` and add the statement to create an aneka application. 
        ``` c#
        static void Main(string[] args)
        {
            AnekaApplication<AnekaThread, ThreadManager> app = null;
            try
            {
                Logger.Start();
             }
            finally
            {
                Logger.Stop();
                app.StopExecution();
            }
        }
        ```
    2. Add the statement under try after `Logger.Start();` to read the aneka configuration file `conf.xml`. Here, make sure to change the location of the `conf.xml` to yours.
    ```c#
    Configuration conf = Configuration.GetConfiguration(@"G:\My Drive\ANEKA\AnekaPrograms\conf.xml");
    ```
    3. Attach the conf file to Aneka application
        ```c#
        app = new AnekaApplication<AnekaThread, ThreadManager>("Simple-Thread-Application",conf);
        ```
    4. Create an object of the class HelloWorld
    ```c#
    HelloWorld hw = new HelloWorld();
    ```      
    5. Create an aneka thread `anekaTh` that will use the method `hw.HelloPrint`
    ```c#
    AnekaThread anekaTh = new AnekaThread(hw.HelloPrint, app);
    ```
    6. Start the thread
    ```c#
    anekaTh.Start();
    ```
    7. Grab the output from the thread using join 
    ```c#
    anekaTh.Join();
    hw = (HelloWorld)anekaTh.Target;
    ```
    8. Print an output in console with other parameters such as Application Id and Node ID where the thread is executed.
    ```c#
    Console.WriteLine("-|Thread output: {0} |Application Name: {1} |Node Id: {2}", hw.result,anekaTh.ApplicationId,anekaTh.NodeId);
    ```
10. Final code should look like
    ```c#
    using System;
    using Aneka;
    using Aneka.Threading;
    using Aneka.Entity;

    namespace FirstThreadApplication
    {
        class Program
        {
            [Serializable]
            public class HelloWorld
            {
                public string result;
                public void HelloPrint()
                {
                    result = "Hello Aneka...";
                }
            }
            static void Main(string[] args)
            {
                AnekaApplication<AnekaThread, ThreadManager> app = null;
                try
                {
                    Logger.Start();
                    // Configuration file
                    Configuration conf = Configuration.GetConfiguration(@"G:\My Drive\ANEKA\AnekaPrograms\conf.xml");
                    //Attch the conf file to Anek aapplication
                    app = new AnekaApplication<AnekaThread, ThreadManager>("Simple-Thread-Application",conf);
                    // Create a object to class
                    HelloWorld hw = new HelloWorld();
                    //Create a Aneka Thread
                    AnekaThread anekaTh = new AnekaThread(hw.HelloPrint, app);
                    //Start Aneka Thread
                    anekaTh.Start();
                    //Get the output from the thread execution
                    anekaTh.Join();
                    hw = (HelloWorld)anekaTh.Target;
                    //Print the output in console
                    Console.WriteLine("-|Thread output: {0} |Application Name: {1} |Node Id: {2} ", hw.result,anekaTh.ApplicationId,anekaTh.NodeId);
                }
                finally
                {
                    Logger.Stop();
                    app.StopExecution();
                }
            }
        }
    }

    ```
11. Save and run the application using `Ctrl+F5` (Debug and Run).
12. Finaly you should see the output in the console dispaying the thread output.
??? Warning "Check the following are correct, if you dont recieve output"
    1. Make sure your Aneka cluster is up and running.
    2. Ping to the Master IP address from developer machine.
    3. Cross check that you modified with the correct IP in `conf.xml`
!!! tip "Try Out"
    1. Try modify the program to recieve the name from the terminal and print the output using aneka thread.
    2. Print the some more metrics in the console such as Submission Time, Completion Time and Maximum Exec. Time

## Some more examples
 
<div class="grid cards" markdown>
-  __[Simple Thread]__ – Create a simple application to display the IP address of a worker machine using Aneka Thread API.
-  __[Multiple Thread Example]__ – Run 100 aneka threads.
-  __[Sort Example]__ – Sort 10 numbers



</div>
  [Simple Thread Example]: simpletask.md
  [Multiple Task Example]: setting-up-navigation.md
  [Gauss Example]: setting-up-the-header.md
  [guide]: ../../OtherTopics/visualstudio.md

