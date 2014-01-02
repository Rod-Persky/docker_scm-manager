# Docker: scm-manager

Sets up a container with scm-manager installed listening on port 8080.

## Usage

To run the container, do the following:

``` bash
docker run -d Rod-Persky/scm-manager
```

This should download the image which has been uploaded to the docker index (pretty big?),
once it is running you can check the process is up via:

``` bash
docker ps
ID                  IMAGE                     COMMAND                CREATED
STATUS              PORTS
f08460ebd8cc        Rod-Persky/scm-manager:1.522   ./scm-server/bin/scm-server   20 minutes
ago      Up 20 minutes       49153->8080
```

Or something like that, the imporant bit is the image tagged `Rod-Persky/scm-manager` is indeed
there. So now your scm-manager instance is available by going to `http://localhost:8080` if not
you can add `docker run -p 127.0.0.1:80:8080 Rod-Persky/scm-manager` which should do the trick.

## Building
To build the image, instead of downloading it (which tends to be a bit large) simply invoke

``` bash
docker build github.com/Rod-Persky/docker_scm-manager
```

Although the caviat in this case is that it's a prebuilt binary, so really it should be as large
as the binary accessories.


# Dockerfile Contents

Dockerfiles are pretty good at being their own documentation, however I don't expect you to
read them and try to understand it. So I've attached the main comments below.

First off we update the OS to ensure that the repository lists contain the correct file versions:

``` bash
RUN export version=$(cat /etc/lsb-release | grep DISTRIB_CODENAME=precise | cut -d '=' -f2) && \
    echo deb http://archive.ubuntu.com/ubuntu $version universe >> /etc/apt/sources.list && \
    echo deb http://archive.canonical.com/ $version partner >> /etc/apt/sources.list && \
    apt-get update
```

Next we install the nessesary components, the distribution supports Git, SVN and HG, so they
are included

``` bash
#                To download SCM                                      For  Mercurial Plugin
#    Install            |                          Mercurial SVN                |
#       |               |                               |                       |
RUN apt-get install -y wget openjdk-7-jre-headless mercurial git subversion python2.7
#                                     |                       |       |
#                                To run SCM                  GIT     SVN
```

Finally we install the program:

``` bash
# Install the program in one big step
RUN  mkdir /usr/share/scm-manager && \
     cd /usr/share/scm-manager && \
     wget 
https://repository-scm-manager.forge.cloudbees.com/snapshot/sonia/scm/scm-server/1.36-SNAPSHOT/scm-server$
     tar -xzf $(ls | grep ".gz") && \
     rm $(ls | grep ".gz")
```

# Interfacing

So there are two important interfaces, the web and the files. As for the files they are managed as:

``` bash
# Note I suggest to use /datay as the location where the repository files can be kept
# as then you can use -b /home/repos:/data to bind the two filesystems together
#                            |          |
#                            |      SCM server
#                       On your computer

VOLUME ["/data"]

# It is then easy to progress and import your own repositories following the scm layout of
# data/hg    <- storage of mercurial repositories
#      git   <- storage of git repositories
#      svn   <- storage of subversion repositories
```

The web interface is managed in exactly the same way, however the default interface is used
so it is connected simply though exposing it as an option. To connect to it, we still need
`docker -p 127.0.0.1:80:8080`

``` bash
EXPOSE 8080
```

Finally there is the command that is launched by default, this is actually the part that we care about
but it's abstracted by docker so we only really have to punch in the docker parameters `docker -b`

``` bash
CMD ["/usr/share/scm-manager/scm-server/bin/scm-server"]
```

So all up to run the program, as mentioned before, we have:

``bash
docker run -b -p 127.0.0.1:80:8080 rodpersky/docker_scm-manager
```

# Maintainer (of this Dock)

* Rodney Persky
