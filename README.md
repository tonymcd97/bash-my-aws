![TravisCI](https://api.travis-ci.org/realestate-com-au/bash-my-aws.svg)

bash-my-aws
===========

Written to make my life easier, bash-my-aws assists Infrastructure Jockeys using 
Amazon Web Services from the command line.

This project provides easily memorable commands for realtime control of resources
in Amazon AWS. The goal is to reduce the time between intention and effect.

The functions are just as happy being called from your scripts as they are being
tapped out on the keyboard.

They make extensive use of the incredibly powerful AWSCLI. It's hoped they may
also provide a useful reference to its use.


## Prerequisites

* bash
* [awscli](http://aws.amazon.com/cli/)
* jp (installed automatically if you used python-pip to install awscli)
* [jq-1.4](http://stedolan.github.io/jq/download/) or later (for stack-diff)


## Installation

As shown below, you may simply clone the GitHub repo and source the files required.
(You should probably fork it instead to keep your customisations)

```ShellSession
$ git clone https://github.com/realestate-com-au/bash-my-aws.git ~/.bash-my-aws
```


## Usage

Source the functions you want with something like:
```ShellSession
$ for f in ~/.bash-my-aws/lib/*-functions; do source $f; done
```

Add the bash_completion scripts: (optional)
```ShellSession
$ source <(~/.bash-my-aws/bin/generate_bash_completion)
```

Typing stack[TAB][TAB] will list available functions for CloudFormation:

```ShellSession
$ stack
stack             stack-elbs        stack-parameters  stack-update
stack-asgs        stack-events      stack-resources   stack-validate
stack-create      stack-failure     stack-status      stacks
stack-delete      stack-instances   stack-tail
stack-diff        stack-outputs     stack-template
```

For more info on the query syntax used by AWSCLI, check out http://jmespath.org/tutorial.html


### Usage examples

**cloudformation-functions**

#### Create a stack

This function gives you tab completion for filenames (missing from AWSCLI).

```ShellSession
$ stack-create
USAGE: stack-create stack

$ stack-create example      # creates stack called example using example.json
```

It's also one of the functions that allows you to omit the template name
if it exists in the current directory and matches the stack name with '.json'
appended.

It's even smart enough to detect that you've added '-blah' to the stack name.
```ShellSession
$ stack-create example-test # creates stack called example-test using example.json
```


#### List stacks

This is basically 'ls' with the ability to filter by a search string

```ShellSession
$ stacks # call without filter argument to return all stacks
example-app
example-app-test
example-app-dev
something
something-else

$ stacks example # Or filter out the relevant stacks
example-app
example-app-test
example-app-dev
```


#### See what changes will be made by updating a stack
```ShellSession
$ stack-diff
USAGE: stack-diff stack [template-file]

$ stack-diff example-dev
template for stack (example) and contents of file (example-dev.json) are the same

e--- params
+++ example-params-dev.json
@@ -4,7 +4,7 @@
         "ParameterKey": "slipGeneratorRolePath"
     },
     {
-        "ParameterValue": "something",
+        "ParameterValue": "something-else",
         "ParameterKey": "storageBucketName"
     },
     {

#### Updating a stack

```ShellSession
$ stack-update
USAGE: stack-update stack [template-file] [params-file]

$ stack-update example # creates stack called example using example.json
...
```


#### Deleting a stack

```ShellSession
$ stack-delete
USAGE: stack-delete stack

$ stack-delete example # deletes stack called example
...
```


#### Tailing stack events

The create/update tasks call this one but it can also be called directly.
It watches events for a stack until it sees them complete or fail.

```ShellSession
$ stack-tail my-stack
---------------------------------------------------------------------------------------------------------------------------------------------------------
|                                                                  DescribeStackEvents                                                                  |
+--------------------------+-------------------------------+-------------------------------------------+------------------------------------------------+
|  2015-07-25T23:13:21.628Z|  MyStack                      |  AWS::CloudFormation::Stack               |  CREATE_IN_PROGRESS                            |
|  2015-07-25T23:13:27.221Z|  AppServerSSHSecurityGroup    |  AWS::EC2::SecurityGroup                  |  CREATE_IN_PROGRESS                            |
|  2015-07-25T23:13:27.235Z|  DeploymentSQSQueue           |  AWS::SQS::Queue                          |  CREATE_IN_PROGRESS                            |
|  2015-07-25T23:13:27.291Z|  InternalELBSecurityGroup     |  AWS::EC2::SecurityGroup                  |  CREATE_IN_PROGRESS                            |
|  2015-07-25T23:13:27.537Z|  AppServerRole                |  AWS::IAM::Role                           |  CREATE_IN_PROGRESS                            |
|  2015-07-25T23:13:28.244Z|  DeploymentSQSQueue           |  AWS::SQS::Queue                          |  CREATE_IN_PROGRESS                            |
|  2015-07-25T23:13:28.769Z|  DeploymentSQSQueue           |  AWS::SQS::Queue                          |  CREATE_COMPLETE                               |
+--------------------------+-------------------------------+-------------------------------------------+------------------------------------------------+
```
