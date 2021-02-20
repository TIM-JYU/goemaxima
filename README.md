goemaxima
=========
This project developed at the Mathematical Institute of Goettingen provides an alternative webservice for stack, which is a question type for ilias and moodle focused on math.
It is mainly intended to be used as a docker container in a cluster, optimally one supporting autoscaling, as scaling in the service itself is not supported.

This implementation in golang strives to have faster startup and processing times than the corresponding java version by using fork instead of starting a new process for each request.
For some more information on how this works, [see the documentation](/doc/How_it_works.md).


Building a Docker Image
=======================

A docker image can be built by first building the web server with buildweb.sh, which will place the web server executable into `./bin/`.
In order for that to work, `go` needs to be installed.

After that, the docker container for a particular stackmaxima version can be built by invoking `buildimage.sh STACKMAXIMA_VERSION` with `STACKMAXIMA_VERSION` substituted to be the stackmaxima version to base the container off.

Example:
```
$ ./buildweb.sh
$ ./buildimage.sh 2020113000
```

The image should then be available as `goemaxima:2020113000-dev`.

You can also push your built image to a registry of your choice using the second argument. For example:

```
$ ./buildimage.sh 2020113000 timimages
```

This will push `goemaxima:2020113000-dev` to `timimages` registry. If you want to push a non-dev tag, specify the tag using the thrid argument:

```
$ ./buildimage.sh 2020113000 timimages 1.0.3
```

This will build an image, tag it as `goemaxima:2020113000-1.0.3` AND `goemaxima:2020113000-latest` and push it to the chosen registry.


Adding new stackmaxima versions
===============================

Stackmaxima refers to the Maxima library that Stack assesment system adds on top of Maxima to allow evaluating answers.  
When updating stackmaxima, you will likely have to also build a new version of goemaxima.
To do this:

1. Obtain stackmaxima files that you want to use
    These files are usually located in `stack/maxima` folder of Stack (or moodle-qtype_stack).  
    For example: https://github.com/maths/moodle-qtype_stack/tree/master/stack/maxima

    While you're at it, write down the version of stackmaxima which is usually located in `stackmaxima.mac` under `stackmaximaversion` version.  
    For example: https://github.com/maths/moodle-qtype_stack/blob/master/stack/maxima/stackmaxima.mac#L3067
2. Add stackmaxima files to the repo
    In this repo, create a folder `stack/<stackmaximaversion>` where `<stackmaximaversion>` is the version of stackmaxima you're adding.
    
    Copy all stackmaxima files into the folder you created.
3. Add stackmaxima version definition to `versions`
    Open `versions` file that's located in this repo. The file is a tab-separated values (TSV) file that contains definitions of all buildable stackmaxima variations.

    Add a new line to the file in the form
    ```
    <stackmaximaversion>	<maximaversion>	<sbclversion>
    ```
    where
    
    * `<stackmaximaversion>` is version of stackmaxima that you obtained
    * `<maximaversion>` is version of Maxima to use in the image
    * `<sbclversion>` is version of Steel Bank Common Lisp to use in the image (used to build Maxima)
    
    Versions for Maima and SBCL vary and can usually be obtained from [Stack Docs](https://docs.stack-assessment.org/en/Installation/).

Using the Docker Image
======================

The prebuilt docker images are available from the docker hub at `timimages/goemaxima:[version]-latest`.

The port of the server in the container is `8080`. You can use one of the following paths

License
=======
goemaxima is licensed under the GPLv3.
