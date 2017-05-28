# RAPID - Android Demo Application
This is part of the [RAPID Project](http://www.rapid-project.eu) and is an ongoing work. While RAPID envisions to support heterogeneous devices, this is the demonstration of the tasks offloading on **Android**. This demo uses the [RAPID Android Offloading Framework](https://github.com/RapidProjectH2020/rapid-android).  
For Java and CUDA code offloading on other platforms, have a look at the generic [RAPID Linux/Windows Demo Application](https://github.com/RapidProjectH2020/rapid-linux-DemoApp).

In this page we will guide you on how to:
* [Quickly Install and Test the Demo Application](#installing-and-testing-the-demo).
* [Start Developing Android Applications with RAPID Offloading Support](#developing-android-applications-with-rapid-offloading-support).

## Intro
RAPID enables automatic computation offloading of heavy tasks on Android and Linux/Windows Java applications.
Moreover, RAPID enables the possibility for embedding CUDA code in applications for generic Android devices
and for Java Linux/Windows.  
RAPID enables highly CPU- or GPU-demanding applications to be offered through physical or virtual devices with lower capabilities or resources than the applications require, potentially backed by remote accelerators of the same class (D2D) or higher classes (hierarchical mode).  
RAPID supports its acceleration service through code offloading to more capable devices or devices with more resources, when this is deemed necessary or beneficial.
<p align="center">
<img src="http://rapid-project.eu/files/rapid-arch.png" width="480">
</p>

### Terminology
* **User Device (UD):** is the low-power device (phone, e.g.) that will be accelerated by code offloading. In our scenario it will be a phone running Android (version 4.1+ is recommended).
* **Acceleration Client (AC):** is an Android library that enables code offloading on the Android applications.
* **Application (AP):** is the Android application that will be accelerated by the framework. This application includes the AC as a library and uses the AC's API and the RAPID programming model.
* **VM:** is a Virtual Machine running on virtualized software, with the same operating system as the UD. In our scenario it will be an Android-x86 instance (version 4.0+ is recommended) running on VirtualBox.
* **Acceleration Server (AS):** is an Android application that runs on the VM and is responsible for executing the offloaded code by the client.

### Description of the Demo App
The demo application shows three representative use case offloading scenarios:

* **Android Java method offloading.**

  This is the simplest case of computation offloading, dealing with remote execution of Java methods.
  We have selected the [N-Queens puzzle](https://developers.google.com/optimization/puzzles/queens) as a representative for this use case.
  The N-Queens puzzle is the task of *arranging N chess queens in the chess keyboard so that no two queens can attack each other*.
  The current implementation is a brute force algorithm.
  The user can vary the number of queens from 4 to 8, changing the difficulty of the problem and its duration.
  When pressing the button "Solve Nqueens", the computation will be performed via the RAPID AC locally on the device or remotely on the VM.
  Cumulative statistics in terms of number of local/remote executions and average duration of local/remote executions will be shown to the user in real time.
  The expected result is that while increasing the number of queens, the gap between the local and remote execution should increase.

* **Android C/C++ native function offloading.**

  Android allows developers to include native C/C++ code in their applications for increasing the performance 
  of intensive tasks or for allowing code reusability. 
  A normal Java method can call a native function thanks to the Java Native Interface (JNI). 
  To show that RAPID supports offloading of native functions, we have included in the demo a simple application 
  that simply returns the string "Hello from JNI" implemented in C++ and included as a native library in the demo application.
  Also in this case, the user can see cumulative statistics in terms of number and duration of local/remote execution.
  The expected result here is that the local execution will always be faster than the remote one, 
  given that the native function is not computationally intensive, meaning that the remote execution is penalized by the data transmission.
  However, this is just a simple demo that users can use as a starting point for building their applications 
  including offloadable native functions.

* **Android CUDA programming and Android CUDA offloading.**

  The third showcase is the most complex one, including CUDA code offloading.
  The demo application in this case is a matrix multiplication performed using CUDA.
  Notice that CUDA development is not possible for the majority of Android devices.
  As such, the developer implements her CUDA code in a development machine as if it were for another supported operating system,
  e.g. Linux, and generates the Parallel Thread Execution (PTX) file using the NVIDIA CUDA Compiler (nvcc).
  Then, the PTX file has to be embedded in the assets/cuda-kernels folder of the Android application, 
  where the GVirtuS Frontend will look for loading the file during runtime.
  When the execution of the method containing CUDA calls is performed locally, if the client device does not have a GPU, 
  the GVirtuS Frontend will offload the CUDA calls from the client device to the GVirtuS Backend, 
  which will take care of running them on the physical GPU of the machine where it is deployed (i.e. the RAPID cloud). 
  When the execution of the method containing the CUDA calls is performed remotely, because it is offloaded by the RAPID AC,
  the CUDA calls will be executed by the GVirtuS Frontend on the remote VM.
  Given that the VMs do not have a physical GPU, also in this case the GVirtuS Frontend will offload the execution to the GVirtuS Backend.
  However, in this case the GVirtuS Frontend and Backend could potentially be running on the same physical machine 
  (when using the RAPID architecture), which means that the latency of each CUDA call will be smaller than the local case.
  Also in this case, the user can see the cumulative statistics as for the other applications.

<p align="center">
<img src="http://rapid-project.eu/files/rapid-android-demo1.png" width="240">
</p>

## Installing and Testing the Demo
The demo will show how portion of the application's code can be run locally on the device or can be offloaded on a remote VM.
Installation steps:
1. Clone this project on Android Studio.
2. Install the demo application in an Android device (a phone with Android 4.1+ is recommended).
3. Install the Android-x86 VM we provide on a computer that is reachable by the phone device (i.e. the phone can ping the VM).
   * Install [VirtualBox](https://www.virtualbox.org/) on the computer.
   * Download the VirtualBox image of the VM from the RAPID website [here](http://rapid-project.eu/files/android-x86-6.0-r3.ova).
   * [Import the image on VirtualBox](https://docs.oracle.com/cd/E26217_01/E26796/html/qs-import-vm.html).
   * *In the final release of the RAPID architecture we will provide VMs running on the RAPID cloud,
   meaning that you will not have to deal with these steps yourself.*
4. Launch the Android VM you just imported.
   * The VM will automatically start the AS, you don't have to do anything.
   * Note the IP of the VM.
     * ssss

You can choose the execution location of the tasks using the radio buttons:
* `Always Local` will instruct the framework to always execute the tasks locally on the device (phone).
* `Always Remote` will instruct the framework to always execute the tasks remotely on the VM.
* `Energy and Delay` will instruct the framework to make dynamic decisions and choose the execution location (local or remote) so that to minimize the energy and execution time of each task.


## Developing Android Applications with RAPID Offloading Support
