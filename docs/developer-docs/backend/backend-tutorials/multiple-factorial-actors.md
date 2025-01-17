# Using multiple actors

February 2020 (Alpha) :proglang: Motoko :IC: Internet Computer :company-id: DFINITY

In this tutorial, you are going to create a project with multiple actors. Currently, you can only define one actor in a Motoko file and a single actor is always compiled to a single canister. In addition, you cannot yet call functions defined in an actor in one canister from an actor defined in another canister or define an actor class to support multiple actor instances in your Motoko programs. You can, however, create **projects** that have multiple actors and can build multiple canisters from the same `dfx.json` configuration file.

For this tutorial, you are going to create separate program files for three actors in the same project. This project defines the following unrelated actors:

-   The `assistant` actor provides functions to add and show tasks in a to-do list.

    For simplicity, the code sample for this tutorial only includes the functions to add to-do items and to show the current list of to-do items that have been added. A more complete version of this program-with additional functions for marking items as complete and removing items from the list—is included in [Sample code, applications, and microservices](./sample-apps.md).

-   The `factorial` actor provides a function for determining the factorial for a specified number.

-   The `daemon` actor provides mock functions for starting and stopping a daemon.

    This code sample simply assigns a variable and prints messages for demonstration purposes.

## Before you begin

Before starting the tutorial, verify the following:

-   You have downloaded and installed the SDK as described in [the Quick Start](/tutorials/deploy_sample_app.md).

-   You have stopped any network replica processes running on the local computer.

This tutorial takes approximately 20 minutes to complete.

## Create a new project

To create a new project for this tutorial:

1.  Open a terminal shell on your local computer, if you don’t already have one open.

2.  Change to the folder you are using for your IC projects, if you are using one.

3.  Create a new project by running the following command:

        dfx new multiple_actors

4.  Change to your project directory by running the following command:

        cd multiple_actors

## Modify the default configuration

You have already seen that creating a new project adds a default `dfx.json` configuration file to your project directory. For this tutorial, you need to add sections to this file to specify the location of each program that defines an actor you want to build.

To modify the default `dfx.json` configuration file:

1.  Open the `dfx.json` configuration file in a text editor, then change the default `multiple_actors` canister name and source directory to `assistant`.

    For example:

        {
          "canisters": {
            "assistant": {
              "frontend": {
                "entrypoint": "src/multiple_actors/public/index.js"
              },
              "main": "src/assistant/main.mo"
            },

    Because you are going to add settings to this `canisters` section of the configuration file, you must also add a **comma** after the curly brace that encloses the location of the `assistant` main source code file.

2.  Add a new canister name and source file location for the `factorial` program and a new canister name and source file location for the `daemon` program files below the `assistant` source file location.

    For example:

            "factorial": {
              "main": "src/factorial/main.mo"
            },
            "daemon": {
              "main": "src/daemon/main.mo"
                }
          },

    You can leave the other sections as-is.

3.  Change the name of the default source file directory to match the name specified in the `dfx.json` configuration file by running the following command:

        cp -r src/multiple_actors/ src/assistant/

4.  Copy the `assistant` source file directory to create the main program file for the `factorial` actor by running the following command:

        cp -r src/assistant/ src/factorial/

5.  Copy the `assistant` source file directory to create the main program file for the `daemon` actor by running the following command:

        cp -r src/assistant/ src/daemon/

## Modify the default template programs

You now have three separate directories in the `src` directory, each with a template `main.mo` file. For this tutorial, you will replace the content in each template `main.mo` file with a different actor.

To modify the default template source code:

1.  Open the `src/assistant/main.mo` file in a text editor and delete the existing content.

2.  Copy and paste the following sample code into the file:

        include::example$multiple-actors/assistant/main.mo

3.  Open the `src/factorial/main.mo` file in a text editor and delete the existing content.

4.  Copy and paste the following sample code into the file:

        include::example$multiple-actors/factorial/main.mo

5.  Open the `src/daemon/main.mo` file in a text editor and delete the existing content.

6.  Copy and paste the following sample code into the file:

        include::example$multiple-actors/daemon/main.mo

## Build all of the canisters in the project

You now have a program that you can compile into an executable WebAssembly module that you can deploy on your local replica network.

To build the executable for each actor in the project:

1.  Change to the `~/ic-projects/multiple_actors` root directory for your project, if needed.

2.  Build the WebAssembly executable for each program by running the following command:

        dfx build --all

    If the command is successful, it builds all of the canisters you have specified in the `dfx.json` file.

        Building canister assistant
        Building canister factorial
        Building canister daemon

## Deploy the canisters in the project

You now have three separate compiled programs—one for each actor—ready for deployment.

To deploy the canisters:

1.  Start the IC network on your local computer by running the following command:

        dfx start

2.  Open a new terminal shell, then change the `~/ic-projects/multiple_actors` root directory for your project.

    For example:

        cd ~/ic-projects/multiple_actors

3.  Deploy your canisters on the local network by running the following command:

        dfx canister install --all

## Verify deployment by calling functions

You now have three programs deployed as a **canisters** on your local replica network and can test each program by using `dfx canister call` commands.

To test the programs you have deployed on the local replica network:

1.  Use the `dfx canister call` command to call the canister `assistant` using the `addTodo` function and pass it the task you want to add by running the following command:

        dfx canister call assistant addTodo '("Schedule monthly demos")'

2.  Verify that the command returns the to-do list item using the `showTodos` function by running the following command:

        dfx canister call assistant showTodos

    The command returns output similar to the following:

        ("
        ___TO-DOs___
        (1) Schedule monthly demos

3.  Use the `dfx canister call` command to call the canister `factorial` using the `fac` function by running the following command:

        dfx canister call factorial fac '(8)'

    The command returns the result of the function:

        (40320)

4.  Use the `dfx canister call` command to call the canister `daemon` using the `launch` function by running the following command:

        dfx canister call daemon launch

5.  Verify the mock `launch` function returns "The daemon process is running" message":

        (""The daemon process is running"")

6.  Stop the IC processes running on your local computer by running the following command:

        dfx stop
