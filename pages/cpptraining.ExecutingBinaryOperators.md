---
title: cpptraining.ExecutingBinaryOperators
---
{% include nav prev="CppTraining#FitNesse" %}
{% include toc %}

## Background
These steps assuem you have already worked through the tutorial: [Getting Started With FitNesse in C++](cpptraining.GettingStartedWithFitNesseInCpp).

## Building the RpnCalcualtor
You can find the source for the RpnCalculator at two github locations:
* [Here is production code](http://github.com/schuchert/RpnCalculatorInCpp)
* [Here is the test code](http://github.com/schuchert/RpnCalculatorInCppTests)

### Building the Basic Structure
* Create a top-level directory for all of your work. I'll be using ~/src/waat
* Under that, create a workspace directory. I'll use ~/src/waat/workspace
{% highlight terminal %}
[~/src]% mkdir waat 
[~/src]% cd waat
/Users/schuchert/src/waat
[~/src/waat]% mkdir workspace
[~/src/waat]% cd workspace 
/Users/schuchert/src/waat/workspace
{% endhighlight %}
* Retrieve the production code using the following command:
{% highlight terminal %}
git clone git://github.com/schuchert/RpnCalculatorInCpp.git
{% endhighlight %}
* The output should resemble:
{% highlight terminal %}
[~/src/waat/workspace]% git clone git://github.com/schuchert/RpnCalculatorInCpp.git
Initialized empty Git repository in /Users/schuchert/src/waat/workspace/RpnCalculatorInCpp/.git/
remote: Counting objects: 67, done.
remote: Compressing objects: 100% (65/65), done.
remote: Total 67 (delta 7), reused 0 (delta 0)
Receiving objects: 100% (67/67), 12.17 KiB, done.
Resolving deltas: 100% (7/7), done.
[~/src/waat/workspace]% ls
RpnCalculatorInCpp/
{% endhighlight %}
* Retrieve the test code using the command: 
{% highlight terminal %}
git clone git://github.com/schuchert/RpnCalculatorInCppTests.git
{% endhighlight %}
* The output should resemble:
{% highlight terminal %}
[~/src/waat/workspace]% git clone git://github.com/schuchert/RpnCalculatorInCppTests.git
Initialized empty Git repository in /Users/schuchert/src/waat/workspace/RpnCalculatorInCppTests/.git/
remote: Counting objects: 37, done.
remote: Compressing objects: 100% (36/36), done.
remote: Total 37 (delta 9), reused 0 (delta 0)
Receiving objects: 100% (37/37), 8.87 KiB, done.
Resolving deltas: 100% (9/9), done.
[~/src/waat/workspace]% ls
RpnCalculatorInCpp/        RpnCalculatorInCppTests/
[~/src/waat/workspace]% 
{% endhighlight %}

### Configuring the Eclipse Project
In [previous tutorial](cpptraining.GettingStartedWithFitNesseInCpp), you created a directory called ~/src/cslim and under that is CppUTest. To create this project, we'll use the equivalent of environment variables in Eclipse.
* Create a new workspace. Select the directory containing two github projects you just checked out. In my case that's **Users/schuchert/src/waat/workspace**
* Close the Welcome to Eclipse tab.
* Edit the Eclipse properties:
  * Under General:Workspce, enable **Refresh automatically**
  * Under Eclipse properties:C/C++:Build:Environment, define some environment variables:
    * **CSLIM_BASE** = **/Users/schuchert/src/cslim/cslim
    * **CPPUTEST_BASE** = **/Users/schuchert/src/cslim/cpputest
  * On my Mac, I additionally set:
    * **GPP** = **/usr/local/bin/g++
  * This allows me to use g++ 4.4 or 4.5 instead of 4.2
* Apply those changes.
* Next, select **File:Import**
* Under **General** select **Existing Projects into Workspace**
* Click **Next**
* Click the **Browse** button then simply press OK
* You should see listed two projects
  * RpnCalculatorInCpp
  * RpnCalculatorInCppTests
* Both of these projects should be selected by default.
* Click **Finish**
* Eclipse will probably attempt to build **RpnCalculatorInCppTests**, but it will fail because the other project must be build first.
* Pull down the **Project** menu and select **Build All**
* This should build the library libRpnCalculatorInCpp.a first and then build the executable RpnCalculatorInCppTests
* When this is done, right-click on the RpnCalculatorInCppTests projects, select **RunAs::Local C/C++ Application**

You should see your tests pass. As of this writing, there are 70 tests:
{% highlight terminal %}
..................................................

....................

OK (70 tests, 70 ran, 108 checks, 0 ignored, 0 filtered out, 8 ms)
{% endhighlight %}
## Creating Your First Test Table
Since you've already worked through [Getting Started With FitNesse in C++](cpptraining.GettingStartedWithFitNesseInCpp), you have FitNesse installed somewhere. Start your FitNesse instance. Here's what it looks like on my computer:
{% highlight terminal %}
[~]% cd src/cpp_fitnesse 
/Users/schuchert/src/cpp_fitnesse
[~/src/cpp_fitnesse]% java -jar fitnesse.jar -p 8080
FitNesse (v20100711) Started...
    port:              8080
    root page:         fitnesse.wiki.FileSystemPage at ./FitNesseRoot
    logger:            none
    authenticator:     fitnesse.authentication.PromiscuousAuthenticator
    html page factory: fitnesse.html.HtmlPageFactory
    page version expiration set to 14 days.
{% endhighlight %}
### Creating Top-Level Test Suite
Now that you have FitNesse started, create a top-level page for all of your work.
* Edit the following URL: <http://localhost:8080/RpnExamples>
* Edit its content to be:

{% highlight terminal %}
!contents -R2 -g -p -f -h

!define TEST_SYSTEM {slim}
!define TEST_RUNNER {/Users/schuchert/src/waat/workspace/RpnCalculatorFixtures/Debug/RpnCalculatorFixtures}
{% raw %}
!define COMMAND_PATTERN {%m}
{% endraw %}
{% endhighlight %}
* Confirm this page has a **Suite** button. If not, edit its properties so it is a suite and save those properties.

The TEST_RUNNER makes reference to a project/executable we have not yet created. Don't worry, it'll be there soon.
### Creating First Test
* Go to the following URL and click edit: <http://localhost:8080/RpnExamples.BinaryOperatorExecution>
* Click on the **Edit** button and set its contents to:
{% highlight terminal %}
!|ExecuteBinaryOperator    |
|lhs|rhs|operator|expected?|
|3  |4  |-       |-1       |
|5  |6  |*       |30       |
{% endhighlight %}

* This project is neither a test or suite page by default (it has to do with the page's name). So click on its **Properties** button, select the **Test** radio button and then click on **Save Properties**

This page makes reference to a fixture that does not yet exist. As in the previous step, don't worry. That's next.

## Creating Fixture Project
Now you're going to add a third project to contain your fixtures. This involves creating a project, linking to the calculator.
* Select **File:New:C++ Project**
* Create an **Empty Project**
* Under **Toolchains** either select **Mingw** or some other "working" toolchain. As of this writing, the cygwin will work but you cannot use a debugger.
* For the project name, enter ** RpnCalculatorFixtures**, which you can infer from the TEST_SYSTEM variable set on the top-level page.
* The provided code uses features of G++ 4.4 and above, specifically the compiler flag**-std=c++0x**. The primary thing is the use of the standard class shared_ptr. As of the TR1 standard, this class' namespace is std::tr1::. In the upcoming standard, the class is additionally in std::. To fix this, edit the project's properties. Under C/C++ Build:Settings/C++  Compiler: Miscellaneous, add -std=c++0x (that's zero, not o) to the Other flags.
You'll need to copy in two boilerplate files, create the fixture class and then set up libraries and include paths.
* Add a new file called **Main.c** and set its contents:

{% highlight cpp %}
#include "SocketServer.h"
#include "SlimConnectionHandler.h"
#include "TcpComLink.h"
#include <stdlib.h>
#include <string.h>
#include "SlimList.h"
#include "SlimListDeserializer.h"
#include "StatementExecutor.h"
#include "ListExecutor.h"
#include "SlimListSerializer.h"

void AddFixtures(StatementExecutor* executor);
char * handle_slim_message(char * message);

int connection_handler(int socket)
{
    int result = 0;
    TcpComLink * comLink = TcpComLink_Create(socket);
    SlimConnectionHandler* connection = SlimConnectionHandler_Create(&TcpComLink_send, &TcpComLink_recv, (void*)comLink);
    SlimConnectionHandler_RegisterSlimMessageHandler(connection, &handle_slim_message);

    result = SlimConnectionHandler_Run(connection);

    SlimConnectionHandler_Destroy(connection);
    TcpComLink_Destroy(comLink);
    
    return result;
}

static StatementExecutor * statementExecutor;
static ListExecutor * listExecutor;
int main(int ac, char** av)
{
    statementExecutor = StatementExecutor_Create();
    AddFixtures(statementExecutor);
    
    listExecutor = ListExecutor_Create(statementExecutor);
    
    SocketServer* server = SocketServer_Create();
    SocketServer_register_handler(server, &connection_handler);
        
    int result = SocketServer_Run(server, av[1]);
    
    SocketServer_Destroy(server);
    ListExecutor_Destroy(listExecutor);
    StatementExecutor_Destroy(statementExecutor);
    return result;
}

char * handle_slim_message(char * message)
{
    SlimList* instructions = SlimList_Deserialize(message);
    SlimList* results = ListExecutor_Execute(listExecutor, instructions);
    char * response = SlimList_Serialize(results);
    SlimList_Destroy(results);
    SlimList_Destroy(instructions);
    return response;
}
{% endhighlight %}

You'll notice several warnings about unknown header files. Let's fix that before moving on:
* Edit the project's properties.
* Under **C/C++ Build:Settings**, select the C Compiler settings (That's C not C++!)
* Select the **Includes** and make the following additions:
  *  **"${CSLIM_BASE}/include/CSlim"**
  *  **"${CSLIM_BASE}/include/Com"**
* Save your changes (click **Apply** then **OK**, or just **OK** if you're feeling lucky)

Next, create another new file called **Fixtures.c**: 

{% highlight cpp %}
#include "Fixtures.h"

SLIM_FIXTURES
  SLIM_FIXTURE(ExecuteBinaryOperator)
SLIM_END
{% endhighlight %}

I'm having you preemptively add in the name of a fixture you have yet to write.

Now it's time to create the fixture. Since this is a mechanics tutorial, I'll give you the fixture source code:
#### ExecuteBinaryOperator.cpp

{% highlight cpp %}
#include <stdlib.h>
#include <stdio.h>
#include <string>
#include "RpnCalculator.h"
#include "OperatorFactory.h"
#include "Fixtures.h"
#include "SlimUtils.h"
 
struct ExecuteBinaryOperator {
    ExecuteBinaryOperator() {
        lastValue[0] = 0;
    }

    int execute() {
        RpnCalculator calculator(factory);
        calculator.enterNumber(lhs);
        calculator.enterNumber(rhs);
        calculator.executeOperator(op);
        return calculator.getX();
    }

    static ExecuteBinaryOperator* From(void *fixtureStorage) {
        return reinterpret_cast<ExecuteBinaryOperator*>(fixtureStorage);
    }

    OperatorFactory factory;
    int lhs;
    int rhs;
    std::string op;
    char lastValue[32];
};

extern "C" {
void* ExecuteBinaryOperator_Create(StatementExecutor* errorHandler, SlimList* args) {
    return new ExecuteBinaryOperator;
}
 
void ExecuteBinaryOperator_Destroy(void* self) {
    delete ExecuteBinaryOperator::From(self);
}
 
static char* setLhs(void* fixture, SlimList* args) {
    ExecuteBinaryOperator *self = ExecuteBinaryOperator::From(fixture);
    self->lhs = getFirstInt(args);
    return self->lastValue;
}
 
static char* setRhs(void* fixture, SlimList* args) {
    ExecuteBinaryOperator *self = ExecuteBinaryOperator::From(fixture);
    self->rhs = getFirstInt(args);
    return self->lastValue;
}
 
static char* setOperator(void *fixture, SlimList* args) {
    ExecuteBinaryOperator *self = ExecuteBinaryOperator::From(fixture);
    self->op = getFirstString(args);
    return self->lastValue;
}
static char* expected(void* fixture, SlimList* args) {
    ExecuteBinaryOperator *self = ExecuteBinaryOperator::From(fixture);
    int result = self->execute();
    snprintf(self->lastValue, sizeof(self->lastValue), "%d", result);
    return self->lastValue;
}
 
SLIM_CREATE_FIXTURE(ExecuteBinaryOperator)
    SLIM_FUNCTION(setLhs)
    SLIM_FUNCTION(setRhs)
    SLIM_FUNCTION(setOperator)
    SLIM_FUNCTION(expected)
SLIM_END
 
}
{% endhighlight %}

You'll notice a few more warnings about unknown include files. You'll add a few more include directories, this time under the C++ tab instead of the C Tab
* Edit the project's properties
* Select **C/C++ Build:Settings**
* Now select **GCC C++ Compiler** (or similar) and under **Includes** add:
####  **${workspace_loc:/RpnCalculatorInCpp}
####  **"${CSLIM_BASE}/include/CSlim"
* Apply those changes.

Notice that there's still one missing header file. This is in another library that I've written to make writing C++ cslim fixtures a bit easer:
* Go back to your workspace directory
* Clone the CSlimCppExtensions project from github with the following command: 

{% highlight terminal %}
git clone git://github.com/schuchert/CSlimCppExtensions.git
{% endhighlight %}
* Here's what that will look like:

{% highlight terminal %}
[~/src/waat/workspace]% git clone git://github.com/schuchert/CSlimCppExtensions.git
Initialized empty Git repository in /Users/schuchert/src/waat/workspace/CSlimCppExtensions/.git/
remote: Counting objects: 16, done.
remote: Compressing objects: 100% (14/14), done.
remote: Total 16 (delta 1), reused 0 (delta 0)
Receiving objects: 100% (16/16), 5.95 KiB, done.
Resolving deltas: 100% (1/1), done.
[~/src/waat/workspace]% 
{% endhighlight %}

* Back in Eclipse, **File:Import**
* Select **General:Existing Projects into Workspace**
* Click **Browse** and then **Ok**
* There should be one project listed, **CSlimCppExtensions**
* Click on **Finish**
* Select the project, right-click and select build.
* **Warning**: As of this writing, there is a "missing" method in the cslim library. You'll need to make two changes to the cslim library that you've downloaded to resolve this.

#### Updating CSlim Library
#### <cslim_base>/include/CSlim/SlimListSerializer.h
Add the following function declaration to the header file:

{% highlight cpp %}
void SlimList_Release(char *serializedList);
{% endhighlight %}

#### <cslim_base>/src/CSlim/SlimListSerializer.h
Add a function declaration to the source file:

{% highlight cpp %}
void SlimList_Release(char *serializedList) {
  if(serializedList != 0)
    free(serializedList);
}
{% endhighlight %}

Now that you have the required missing project, you need to add it as a dependent project and include its header files in the RpnCalcualtorFixtures project:
* Edit the properties of RpnCalculatorFixtures
* Under the top-level **Project References**, select **CSlimCppExtensions**
* Under **C/C++ Build:Settings**, select the C++ Compiler settings
* Add under include the following directory: **${workspace_loc:/CSlimCppExtensions}**

Now if you try to build the project, it will compile but it will not link.
* Edit the project's settings
* Find the linker settings under **C/C++ Build:Linker**
* Edit the **Libraries** and add the following list:
####  **CSlim
####  **RpnCalculatorInCpp
####  **CppUTest
####  **CSlimCppExtensions
* Edit he **Library search path** and add the following list:
####  **${workspace_loc:/RpnCalculatorInCpp/Debug}
####  **${CSLIM_BASE}/lib
####  **${CPPUTEST_BASE}/lib
####  **${workspace_loc:/CSlimCppExtensions/Debug}
* Save your changes and build again

You should now be able to run this executable. If you do, you'll see in red text:

{% highlight terminal %}
getaddrinfo: nodename nor servname provided, or not known
{% endhighlight %}

## Running the Test
You should be able to run your FitNesse test and get to green.
* Go back to the following URL: <http://localhost:8080/RpnExamples>
* Click on the **Suite** button
* You should see all green.
* You might see an error regarding a difference in protocol. That's under construction. The cslim library should be updated in the near future (August 2010 I hope).

## Working with a Script Table
Now it is time to program the calculator. First a test, then the fixture code.

### The Test
Create the following test at: <http://localhost:8080/RpnExamples.SumOfPrimesExample>

{% highlight terminal %}
!|script           |ProgramTheCalculator|
|startProgramCalled|sumOfPrimeFactors   |
|addOperation      |primeFactors        |
|addOperation      |sum                 |
|saveProgram                            |
|enter             |12                  |
|execute           |sumOfPrimeFactors   |
|check             |getX       |7       |
{% endhighlight %}

This creates a new operator called "sumOfPrimesFactors" and then executes it. To make this work, you'll need to create a new fixture and register it.

### Creating the Fixture
Create a new source filed called **ProgramTheCalcualtor.cpp**. Here's the source:

{% highlight cpp %}
#include <stdlib.h>
#include <stdio.h>
#include <string>
#include "RpnCalculator.h"
#include "OperatorFactory.h"
#include "SlimUtils.h"
#include "SlimList.h"
#include "Fixtures.h"

struct ProgramTheCalculator {
    ProgramTheCalculator() : calculator(factory) {
    }

    static ProgramTheCalculator* From(void *fixtureStorage) {
        return reinterpret_cast<ProgramTheCalculator*>(fixtureStorage);
    }

    OperatorFactory factory;
    RpnCalculator calculator;
    char lastResult[32];
};

extern "C" {

void* ProgramTheCalculator_Create(StatementExecutor* errorHandler, SlimList* args) {
    return new ProgramTheCalculator;
}

void ProgramTheCalculator_Destroy(void *fixture) {
    delete ProgramTheCalculator::From(fixture);
}

static char* startProgramCalled(void *fixture, SlimList *args) {
    auto *self = ProgramTheCalculator::From(fixture);
    self->calculator.createProgramNamed(getFirstString(args));
    return 0;
}

static char* addOperation(void *fixture, SlimList *args) {
    auto *self = ProgramTheCalculator::From(fixture);
    self->calculator.addOperator(getFirstString(args));
    return 0;
}

static char* saveProgram(void *fixture, SlimList *args) {
    auto *self = ProgramTheCalculator::From(fixture);
    self->calculator.saveProgram();
    return 0;
}

static char* enter(void *fixture, SlimList *args) {
    auto *self = ProgramTheCalculator::From(fixture);
    self->calculator.enterNumber(getFirstInt(args));
    return 0;
}

static char* execute(void *fixture, SlimList *args) {
    auto *self = ProgramTheCalculator::From(fixture);
    self->calculator.executeOperator(getFirstString(args));
    return 0;
}

static char* getX(void *fixture, SlimList *args) {
    ProgramTheCalculator *self = ProgramTheCalculator::From(fixture);
    snprintf(self->lastResult, sizeof(self->lastResult), "%d", self->calculator.getX());
    return self->lastResult;
}

SLIM_CREATE_FIXTURE(ProgramTheCalculator)
    SLIM_FUNCTION(startProgramCalled)
    SLIM_FUNCTION(addOperation)
    SLIM_FUNCTION(saveProgram)
    SLIM_FUNCTION(enter)
    SLIM_FUNCTION(execute)
    SLIM_FUNCTION(getX)
SLIM_END

}
{% endhighlight %}

To get this working, you'll need to register the fixture by updated **Fixtures.c**:

{% highlight cpp %}
#include "Fixtures.h"

SLIM_FIXTURES
  SLIM_FIXTURE(ExecuteBinaryOperator)
  SLIM_FIXTURE(ProgramTheCalculator)
SLIM_END
{% endhighlight %}

Once you make these changes and rebuild, you should have a passing test.

## The Query Table
Here is an example of a query table:

{% highlight terminal %}
!|Query: AlphaNamedOperators|
|op                         |
|dup                        |
|if                         |
|ifElse                     |
|ndup                       |
|nop                        |
|primeFactors               |
|sum                        |
|swap                       |
|drop                       |
{% endhighlight %}

Create this page at: <http://localhost:8080/RpnExamples.AlphaNamedOperatorsExample>

Now you'll need to create a fixture. Create a new source file called **AlphaNamedOperators.cpp**:

{% highlight cpp %}
#include <ctype.h>
#include <stdlib.h>
#include <string>
#include "RpnCalculator.h"
#include "OperatorFactory.h"
#include "Fixtures.h"
#include "SlimUtils.h"
#include "QueryResultAccumulator.h"

struct AlphaNamedOperators {
    OperatorFactory factory;
    RpnCalculator calculator;
    QueryResultAccumulator accumulator;

    AlphaNamedOperators() :
        calculator(factory) {
    }

    ~AlphaNamedOperators() {
    }

    static AlphaNamedOperators* From(void *fixtureStorage) {
        return reinterpret_cast<AlphaNamedOperators*> (fixtureStorage);
    }

    void conditionallyAddOperatorNamed(const std::string &name) {
        if (isalpha(name[0])) {
            accumulator.addFieldNamedWithValue("op", name);
            accumulator.finishCurrentObject();
        }
    }

    char *buildResult() {
        v_string names = calculator.allOperatorNames();
        return buildResult(names);
    }

    char *buildResult(v_string &names) {
        for (v_string::iterator iter = names.begin(); iter != names.end(); ++iter)
            conditionallyAddOperatorNamed(*iter);

        return accumulator.produceFinalResults();
    }
};

extern "C" {
void* AlphaNamedOperators_Create(StatementExecutor* errorHandler,
        SlimList* args) {
    return new AlphaNamedOperators;
}

void AlphaNamedOperators_Destroy(void *fixture) {
    delete AlphaNamedOperators::From(fixture);
}

static char* query(void *fixture, SlimList *args) {
    auto *self = AlphaNamedOperators::From(fixture);
    return self->buildResult();
}

SLIM_CREATE_FIXTURE(AlphaNamedOperators)
    SLIM_FUNCTION(query)SLIM_END

}
{% endhighlight %}

To get this registered, you'll have to update **Fixtures.c**:

{% highlight cpp %}
#include "Fixtures.h"

SLIM_FIXTURES
  SLIM_FIXTURE(ExecuteBinaryOperator)
  SLIM_FIXTURE(ProgramTheCalculator)
  SLIM_FIXTURE(AlphaNamedOperators)
SLIM_END
{% endhighlight %}

Save and rebuild. You should now have a passing test. If you run the entire suite, all three tests should pass.

{% include nav prev="CppTraining#FitNesse" %}
