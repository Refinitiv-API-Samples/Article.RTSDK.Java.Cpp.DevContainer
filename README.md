# How to Develop the Real-Time application inside the 
- version: 1.0.0
- Last update: November 2021
- Environment: Docker
- Compiler: Java
- Prerequisite: [Demo prerequisite](#prerequisite)

## <a id="intro"></a>Introduction

[Visual Studio Code](https://code.visualstudio.com/) (or just VS Code) is a free source code editor developed and maintained by [Microsoft](https://github.com/Microsoft/vscode). This cross-platform editor took over developers' popularity based on its fast and lightweight, supports a variety of programming languages with IntelliSense (a feature that borrows from its sibling, Visual Studio IDE), and supports complete development operations like debugging, task running, and version control.

VS Code provides a lot of extensions that extend the editor features and development workflows (Example: the [REST Client](https://developers.refinitiv.com/en/article-catalog/article/how-to-test-refinitiv-data-platform-rest-api-easily-with-visual-studio-code) and [Thunder Client](https://developers.refinitiv.com/en/article-catalog/article/how-to-test-http-rest-api-easily-with-visual-studio-code---thund) testing tools). The editor also supports the [remote development](https://code.visualstudio.com/docs/remote/remote-overview) that lets you use a container, remote machine, or the [Windows Subsystem for Linux (WSL)](https://docs.microsoft.com/windows/wsl) as a full-featured development environment with the [Remote Development Extension Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack).

As part of the Remote Development Extension Pack, the [Remote - Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) extension lets developers use a Docker container as a full-featured development environment. It allows developer to open any folder inside (or mounted into) a container and take advantage of Visual Studio Code's full feature set (IntelliSense , code navigation, debugging, etc) as a local-quality development experience.

![figure-1](images/01_architecture-containers.png "Visual Studio Code Remote - Containers diagram")

This article demonstrates how to use the VS Code Remote - Containers extension with the Refinitiv Real-Time SDK on the Docker platform as example images. Developers can explorer the SDK building system and example codes on the VS Code editor directly.

**Note**: Please note that the Refinitiv Real-Time SDK isn't qualified on the Docker platform. This article and example projects aim for Development and Testing purposes only. If you find any problems while running it on the Docker platform, the issues must be replicated on bare metal machines before contacting the help-desk support team.

## <a id="Introduction"></a>Refinitiv Real-Time SDK Introduction

[Refinitiv Real-Time SDK](https://developers.refinitiv.com/en/use-cases-catalog/refinitiv-real-time) (RTSDK, formerly known as Elektron SDK) is a suite of modern and open source APIs that aim to simplify development through a strong focus on ease of use and standardized access to a broad set of Refinitiv proprietary content and services via the proprietary TCP connection named RSSL and proprietary binary message encoding format named OMM Message. 

 The SDK source code is available on [GitHub](https://github.com/Refinitiv/Real-Time-SDK), developers can built the RTSDK C++ and Java libraries with the [CMake](https://cmake.org/) and [Gradle](https://gradle.org/) build automation tools respectively. If developers is new to the SDK, they can use the following RTSDK Docker images to set up and learn the SDK quickly.
 * [refinitivapis/realtimesdk_c](https://hub.docker.com/r/refinitivapis/realtimesdk_c): This docker image contains the latest version of the RTSDK C/C++ libraries and examples. 
 * [refinitivapis/realtimesdk_java](https://hub.docker.com/r/refinitivapis/realtimesdk_java): This docker image contains the latest version of the RTSDK Java libraries and examples. 

The images have all dependencies and the building system pre-installed. You can check my colleague's [Introduction to the refinitivapis/realtimesdk_c Docker Image](https://developers.refinitiv.com/en/article-catalog/article/introduction-to-the-refinitivapis-elektronsdkc-docker-image) and [Introduction to the refinitivapis/realtimesdk_java Docker Image](https://developers.refinitiv.com/en/article-catalog/article/introduction-to-the-refinitivapis-realtimesdkjava-docker-image) articles that show a step-by-step guide to deploy and run the RTSDK Docker images via the Docker command line. This article is focusing on access the images with VS Code editor.
 
 If you are using the WebSocket API, there is the [refinitivapis/websocket_api](https://hub.docker.com/r/refinitivapis/websocket_api) docker image that contains the latest version WebSocket API Examples too.

## <a id="prerequisite"></a>Demo prerequisite
This example requires the following dependencies software and libraries.
1. [Visual Studio Code](https://code.visualstudio.com/) editor.
2. [Docker Desktop/Engine](https://docs.docker.com/get-docker/) application.
3. [VS Code - Remote Development extension pack](https://aka.ms/vscode-remote/download/extension)
4. Access to the Refinitiv Refinitiv Data Platform and Refinitiv Real-Time - Optimized. (for the RTO example only)
5. Internet connection. 

I highly recommend you check the extension [System requirements](https://code.visualstudio.com/docs/remote/containers#_system-requirements) and [Installation](https://code.visualstudio.com/docs/remote/containers#_installation) sections to set up your environment.

## <a id="devcontainer_json"></a>A devcontainer.json file

The main configuration file that tells VS Code how to access (or create) a development container (aka Dev container) with a well-defined tool and runtime stack is a ```devcontainer.json``` file. The dev container configuration is either located under ```.devcontainer/devcontainer.json``` or stored as a ```.devcontainer.json``` file (*note the dot-prefix*) in the root of your project.

![figure-2](images/02_devcontainer.png "devcontainer.json file")

**Note**: Make sure to commit a ```.devcontainer``` folder to your version control system.

The basic configuration parameters are as follows:

```
// For format details, see https://aka.ms/devcontainer.json. For config options, see the README at:
// https://github.com/microsoft/vscode-dev-containers/tree/v0.202.5/containers/
{
	"name": "RTSDK_Java",
	"image": "refinitivapis/realtimesdk_java:latest",
}
```
The detail of the configurations above are:
- ```name```: A display name for the container.
- ```image```: The name of an image in a container registry that VS Code should use to create the dev container.

The development container lets you pull images from [DockerHub](https://hub.docker.com/), [GitHub](https://docs.github.com/en/packages/guides/about-github-container-registry), and [Azure](https://azure.microsoft.com/services/container-registry/) Container Registry repositories, and then perform additional tasks such as install development tools (Git, etc.), install VS Code extensions, forward ports, set runtime arguments, etc.

The development container is not limited to Docker images, it supports [Dockerfile](https://code.visualstudio.com/docs/remote/create-dev-container#_dockerfile) and [Docker Compose](https://code.visualstudio.com/docs/remote/create-dev-container#_use-docker-compose) too. You can build own image(s) to match your development requirements, and then share ```Dockerfile``` and/or ```docker-compose.yml``` inside a ```.devcontainer``` folder (with a ```devcontainer.json``` file) to your peer to set up the same development environment.

Please find more details about all devcontainer.json configuration file on the [VS Code - devcontainer.json reference](https://code.visualstudio.com/docs/remote/devcontainerjson-reference) page.

### <a id="rtsdk_devcontainer_json"></a>The RTSDK devcontainer.json file

Let's start with a ```devcontainer.json``` file that pulls the refinitivapis/realtimesdk_java from DockerHub repository. 

```
// For format details, see https://aka.ms/devcontainer.json. For config options, see the README at:
// https://github.com/microsoft/vscode-dev-containers/tree/v0.202.5/containers/
{
	"name": "RTSDK_Java",
	"image": "refinitivapis/realtimesdk_java:latest",
	"workspaceFolder": "/opt/refinitiv/Real-Time-SDK/Java",
}
```
The ```devcontainer.json``` file above sets the following development properties:

- ```name```: Using "RTSDK_Java" as a display name of the container.
- ```image```: Pull "refinitivapis/realtimesdk_java" Docker image from DockerHub [https://hub.docker.com/r/refinitivapis/realtimesdk_java](https://hub.docker.com/r/refinitivapis/realtimesdk_java) URL.
- ```workspaceFolder```: Sets the default path that VS Code should open when connecting to the container. This ```devcontainer.json``` sets the default path to **/opt/refinitiv/Real-Time-SDK/Java** which is the RTSDK Java package location in the container.

### <a id="start_dev_container"></a>Running the Development Container

You can connect to the container and start developing within it by selecting the **Remote-Containers: Reopen in Container** command from the VS Code Command Palette (F1).

![figure-3](images/03_reopen_in_container_1.png "Reopen in Container  menu 1")

Alternatively, the VS Code can detect whether there is a folder contains a Dev container configuration file, and then asks you if you want to reopen folder in a container.

![figure-4](images/04_reopen_in_container_2.png "Reopen in Container  menu 2")

Next, the VS Code window (instance) will reload, pull (or clone) the image, and start building the dev container. Please note that if the image already a

![figure-5](images/05_pulling_container.png "Pull and Build dev container")

After the build completes, VS Code automatically connects to the container at the path we set to the ```workspaceFolder``` property which is the **/opt/refinitiv/Real-Time-SDK/Java** folder.  You can check the VS Code Remote connection status from the button left toolbar.

![figure-6](images/06_dev_container_toolbar.png "Dev Container toolbar")

If you click this toolbar, the VS Code shows the Container Remote connection menu options at the top of the editor.

![figure-7](images/07_dev_container_menu.png "Dev Container menu")

To close the remote connection, choose the "Close Remote Connection" menu.

Now your VS Code and Dev container are ready to run.

### <a id="emajava_running"></a>Running the RTSDK Java Examples

All files and sub-folders are available in the Code explorer. If you open a terminal (``` Ctrl+` ``` or ``` Ctrl+Shift+` ``` - Windows), the current location is  **/opt/refinitiv/Real-Time-SDK/Java** folder too (You can navigate to other locations in the container as well)

![figure-8](images/08_pull_container_success.png "Pull and Build dev container success")

The *refinitivapis/realtimesdk_java* Docker image has built-in all RTSDK Java dependencies and build system, so you can run the SDK examples with the Gradle tool from the VS Code terminal. I am demonstrating with the EMA Java Consumer - Interactive Provider scenario example.

Firstly, open a terminal and run the EMA Java IProvider example 200.

```
$>./gradlew runiprovider200
```

Next, open a new terminal in the same VS Code editor with the ``` Ctrl+Shift+` ``` command, and run the EMA Java Consumer example 200.

```
$>./gradlew runconsumer100
```
The results of EMA Java Consumer - Interactive Provider examples scenario is shown below.

![figure-9](images/09_run_examples.gif "Running Consumer and Interactive Provider examples")

You can run the RTO example, EMA Java ex450_MP_QueryServiceDiscovery with the following command.

```
$>../gradlew runconsumer450 -PcommandLineArgs="-username Machine-ID -password RTO-Password -clientId App_Key -itemName <RIC name>"
```

The result is shown below.

![figure-10](images/10_run_rto_example.gif "Running RTO example")

If you have your own RTDS server in your environment, you can modify the example code to match your RTDS connection requirement. The following example shows the ex100_MP_Streaming example that has been changed the RTDS server endpoint, service name, and RIC name.

![figure-11](images/11_rtds_code.png "Modify the code")

Then, developers can run this example application with ```./gradlew runconsumer100``` command.

Please find more details regarding how to run the RTSDK Java examples on the [RTSDK Java - Quick Start](https://developers.refinitiv.com/en/api-catalog/refinitiv-real-time-opnsrc/rt-sdk-java/quick-start) page.

## <a id="dev_container_folder"></a>File Sharing between the Host and Dev Container

Please note that all changes (newly created/modified files and folders) stay in the Dev container as long as the container is exist (status **Up** and **Exited**). However, if you remove the container (```docker rm```), that container and its data are destroyed. You can additional mount points to add to the container when created for sharing files between the host and Dev container with a devcontainer.json's ```mounts``` attribute.

```
// For format details, see https://aka.ms/devcontainer.json. For config options, see the README at:
// https://github.com/microsoft/vscode-dev-containers/tree/v0.202.5/containers/
{
	"name": "RTSDK_Java",
	"image": "refinitivapis/realtimesdk_java:latest",
	"workspaceFolder": "/opt/refinitiv/Real-Time-SDK/Java",
	"mounts": ["source=${localWorkspaceFolder}/log,target=${containerWorkspaceFolder}/log,type=bind,consistency=cached"]
}

```

The ```devcontainer.json``` file above mounts the current ```<host workspace location>/log``` folder to the ```/opt/refinitiv/Real-Time-SDK/Java/log``` folder in a container. If the folder are not exist (either host or container), the VS Code automatic creates folder(s) for you.

**Caution**: Please note that if you mount a host folder to existing container folder (example: /opt/refinitiv/Real-Time-SDK/Java), the container folder will be replaced by a host folder.

Please find more details regarding the mounts property from the [VS Code - Add another local file mount](https://code.visualstudio.com/remote/advancedcontainers/add-local-file-mount#advancedcontainers-articles) page.

### <a id="emajava_log"></a>Adding EMA Java Log

The best scenario to demonstrate this feature is enabling the EMA API log messages to capture the API and HTTP REST activities. You do not need to close the remote-connection before updating the devcontainer.json. You can choose the "Open Container Configuration File" in the remote connection menu to open the current devcontainer.json file.

![figure-12](images/12_open_configuration.png "Open container config file")

The current devcontainer.json file will be shown in the VS Code editor, you can add the ```"mounts": ["source=${localWorkspaceFolder}/log,target=${containerWorkspaceFolder}/log,type=bind,consistency=cached"]``` configuration to the file.

![figure-13](images/13_add_mounts.png "Adding a mount point")

Next, choose the "Rebuild Container" from the remote connection menu.

![figure-14](images/14_rebuild_container.png "Rebuild container")

After the re-build completes, VS Code automatically the log folders in the host and */opt/refinitiv/Real-Time-SDK/Java/log* folder.  

![figure-15](images/15_ad_mount_success.png "Adding a mount point success")

To enable the EMA Java log file, create a [Java Logging API](https://docs.oracle.com/javase/8/docs/api/java/util/logging/package-summary.html) ```logging.properties``` file in the */opt/refinitiv/Real-Time-SDK/Java/log* folder with the following content:

```
.level=FINEST

handlers=java.util.logging.FileHandler, java.util.logging.ConsoleHandler

java.util.logging.ConsoleHandler.level=FINEST
java.util.logging.ConsoleHandler.formatter=java.util.logging.SimpleFormatter

com.refinitiv.eta.valueadd.reactor.RestReactor.level=FINEST
java.util.logging.FileHandler.level=FINEST
java.util.logging.FileHandler.pattern=/opt/refinitiv/Real-Time-SDK/Java/log/ema.log
 
# Format timestamp as date/time with millisecond
java.util.logging.SimpleFormatter.format=%1$tY-%1$tm-%1$td %1$tH:%1$tM:%1$tS.%1$tL %4$-7s %2$s %n%5$s
```

A logging configuration file sets the EMA API to logs all API messages to the ```/opt/refinitiv/Real-Time-SDK/Java/log/ema.log``` file. 

Finally, run the EMA Java ex450_MP_QueryServiceDiscovery RTO example with the ```-PvmArgs="-Djava.util.logging.config.file=/opt/refinitiv/Real-Time-SDK/Java/log/logging.properties"``` argument.


```
$>../gradlew runconsumer450 -PcommandLineArgs="-username Machine-ID -password RTO-Password -clientId App_Key -itemName <RIC name>" -PvmArgs="-Djava.util.logging.config.file=/opt/refinitiv/Real-Time-SDK/Java/log/logging.properties"
```
Then the API activities, HTTP REST messages log messages are stored inn the ```ema.log``` file, the logging.properties and log files are available in the host machine too.

![figure-16](images/16_log_success.png "Log success")

You can mount other folders to keep the modification codes from the Dev Container back to the host too (note: Do not mount folders to the container existing folders).

### <a id="rtsdk_cpp"></a>Running the RTSDK C++ Image

To run the Dev container with the RTSDK C++ Docker image, you can set a ```devcontainer.json``` content to load the [refinitivapis/realtimesdk_c](https://hub.docker.com/r/refinitivapis/realtimesdk_c) from Docker Hub and change the ```workspaceFolder``` property to the **/opt/refinitiv/Real-Time-SDK/Cpp-C** folder as follows.

```
// For format details, see https://aka.ms/devcontainer.json. For config options, see the README at:
// https://github.com/microsoft/vscode-dev-containers/tree/v0.202.5/containers/
{
	"name": "RTSDK_CPP",
	"image": "refinitivapis/realtimesdk_c:latest",
	"workspaceFolder": "/opt/refinitiv/Real-Time-SDK/Cpp-C",
	"mounts": ["source=${localWorkspaceFolder}/log,target=${containerWorkspaceFolder}/log,type=bind,consistency=cached"]
}
```
Then, run the "Remote-Containers: Rebuild and Reopen in Container" command (or "Remote-Containers: Reopen in Container" if you are running the Dev container for the first time). After the build completes, VS Code automatically connects to the container at the path we set to the ```workspaceFolder``` property which is the **/opt/refinitiv/Real-Time-SDK/Cpp-c** folder with all pre-installed dependencies and build system.

![figure-17](images/17_rtsdkcpp_container.png "Dev container RTSDK C++")

Then you can run the RTSDK C++ example in this Dev Container. If you make some changes to the RTSDK C++ example codes (either ETA or EMA), you can re-build the examples by running a ```make``` command in the container */opt/refinitiv/Real-Time-SDK/rtsdk* folder.

![figure-18](images/18_rtsdkcpp_rto_example.gif "RTSDK C++ EMA RTO")

Please find more details regarding how to run the RTSDK C++ examples on the [RTSDK C++ - Quick Start](https://developers.refinitiv.com/en/api-catalog/refinitiv-real-time-opnsrc/rt-sdk-cc/quick-start) page.

##  <a id="project_files"></a>Project files
This example project contains the following files and folders
1. *.devcontainer/devcontainer.json*: An example development container configuration file.
2. *images*: Project images folder.
3. *LICENSE.md*: Project's license file.
4. *README.md*: Project's README file.

## <a id="how_to_run"></a>How to run the Examples

The first step is to unzip or download the example project folder into a directory of your choice, then set up RTSDK Java or C++ Docker Dev container in a ```devcontainer.json``` file based on your preference.

1. Start a Docker desktop or Docker engine in your machine.
2. Open the project folder in the VS Code editor
3. Install the [VS Code - Remote Development extension pack](https://aka.ms/vscode-remote/download/extension).
4. Open the VS Code Command Palette with the ```F1``` key, and then select the **Remote-Containers: Reopen in Container** command.

![figure-3b](images/03_reopen_in_container_1.png "Reopen in Container  menu 1")

Now the VS Code editor is ready to run RTSDK Java (or C++) Dev container.

## <a id="conclusion"></a>Conclusion and Next Steps

Docker is an open containerization platform for developing, testing, deploying, and running any software application. It helps developers create a consistent development environment without manually maintain dependencies and toolsets for the project. The VS Code [Remote - Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) extension lets developers work with Docker easier by integrating the editor into a Docker container as a full-featured development environment. The extension allows developers open any folder inside (or mounted into) a container and take advantage of VS Code rich feature set.

This example project is just a brief introduction to the Remote - Containers extension. Developers can work with [Dockerfile](https://code.visualstudio.com/docs/remote/create-dev-container#_dockerfile) and [Docker Compose](https://code.visualstudio.com/docs/remote/create-dev-container#_use-docker-compose) to build a customized Docker image that match the development requirements, [debugging](https://code.visualstudio.com/docs/remote/containers#_debugging-in-a-container), install various VS Code extensions to use in the Dev Container (via the [GUI](https://code.visualstudio.com/docs/remote/containers#_managing-extensions) or [configuration file](https://code.visualstudio.com/docs/remote/containers#_adding-an-extension-to-devcontainerjson)), [clone Docker container from Git repository](https://code.visualstudio.com/docs/remote/containers#_quick-start-open-a-git-repository-or-github-pr-in-an-isolated-container-volume), [attach the VS Code to a running container](https://code.visualstudio.com/docs/remote/containers#_attach-to-existing-container), [port forwarding](https://code.visualstudio.com/docs/remote/containers#_always-forwarding-a-port), and much more. I highly recommend you check the following VS Code resources fore more details:
* [Developing inside a Container](https://code.visualstudio.com/docs/remote/containers)
* [Remote development in Containers tutorial](https://code.visualstudio.com/docs/remote/containers-tutorial)
* [Create a development container](https://code.visualstudio.com/docs/remote/create-dev-container)
* [devcontainer.json reference](https://code.visualstudio.com/docs/remote/devcontainerjson-reference)


The [refinitivapis/realtimesdk_c](https://hub.docker.com/r/refinitivapis/realtimesdk_c), [refinitivapis/realtimesdk_java](https://hub.docker.com/r/refinitivapis/realtimesdk_java), and [refinitivapis/websocket_api](https://hub.docker.com/r/refinitivapis/websocket_api) docker images are the good starting points for developers who new to the [Refinitiv Real-Time SDK](https://developers.refinitiv.com/en/use-cases-catalog/refinitiv-real-time). Developers can use the Docker image with the Remote - Containers extensions to set up a development environment and run the SDK examples on the VS Code editor with just a simple steps.  

If you are the developers who already familiar with Maven, I highly recommend you check the [How to deploy and run Real-Time Java Application with Maven in Docker](https://developers.refinitiv.com/en/article-catalog/article/how-to-deploy-and-run-real-time-java-application-with-maven-in-d) that shows how to use Maven to set up the RTSDK JAva development and build environment via Docker

## <a id="ref"></a>References

For further details, please check out the following resources:
* [Refinitiv Real-Time SDK Java page](https://developers.refinitiv.com/en/api-catalog/refinitiv-real-time-opnsrc/rt-sdk-java) on the [Refinitiv Developer Community](https://developers.refinitiv.com/) website.
* [Refinitiv Real-Time SDK Family](https://developers.refinitiv.com/en/use-cases-catalog/refinitiv-real-time) page.
* [Enterprise Message API Java Quick Start](https://developers.refinitiv.com/en/api-catalog/refinitiv-real-time-opnsrc/rt-sdk-java/quick-start)
* [Developer Webinar: Introduction to Enterprise App Creation With Open-Source Enterprise Message API](https://www.youtube.com/watch?v=2pyhYmgHxlU)
* [Developer Article: 10 important things you need to know before you write an Enterprise Real Time application](https://developers.refinitiv.com/article/10-important-things-you-need-know-you-write-elektron-real-time-application)
* [Introduction to the refinitivapis/realtimesdk_c Docker Image](https://developers.refinitiv.com/en/article-catalog/article/introduction-to-the-refinitivapis-elektronsdkc-docker-image)
* [Introduction to the refinitivapis/realtimesdk_java Docker Image](https://developers.refinitiv.com/en/article-catalog/article/introduction-to-the-refinitivapis-realtimesdkjava-docker-image) article.
* [Deploy and Run Refinitiv Real-Time SDK in Docker](https://developers.refinitiv.com/en/article-catalog/article/deploy-and-run-elektron-sdk-docker) article.
* [VS Code: Developing inside a Container](https://code.visualstudio.com/docs/remote/containers) page.
* [VS Code: Remote development in Containers tutorial](https://code.visualstudio.com/docs/remote/containers-tutorial) page.
* [VS Code: Create a development container](https://code.visualstudio.com/docs/remote/create-dev-container) page.
* [VS Code: devcontainer.json reference](https://code.visualstudio.com/docs/remote/devcontainerjson-reference) page.
* [Setting up a VS Code Dev Container](https://benmatselby.dev/post/vscode-dev-containers/) blog post.
* [Hands-On with VSCode & "Dev Containers"](https://dev.to/mcastellin/hands-on-with-vscode-dev-containers-33bf) blog post.
* [Development Acceleration Through VS Code Remote Containers: An Introduction](https://stelligent.com/2020/03/20/development-acceleration-through-vs-code-remote-containers-an-introduction/) blog post.


For any questions related to this article or RTSDK page, please use the Developer Community [Q&A Forum](https://community.developers.refinitiv.com/spaces/71/index.html).