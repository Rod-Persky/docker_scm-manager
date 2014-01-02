# docker-scm-manager

Sets up a container with scm-manager installed listening on port 8080.

## Usage

To run the container, do the following:

``` bash
docker run -d Rod-Persky/docker-scm-manager
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
there. So now your scm-manager instance is available by going to http://localhost:8080 if not
you can add `docker run -p 127.0.0.1:80:8080 Rod-Persky/scm-manager` which should do the trick.

## Building
To build the image, instead of downloading it (which tends to be a bit large) simply invoke

``` bash
docker build github.com/aespinosa/docker-jenkins
```

Although the caviat in this case is that it's a prebuilt binary, so really it should be as large
as the binary accessories.

# Maintainer (of this Dock)

* Rodney Persky
