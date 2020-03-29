## Supported tags and respective `Dockerfile` links

* [`latest` _(Dockerfile)_](https://github.com/fenryxo/nginx-rtmp-docker/blob/master/Dockerfile)

# nginx-rtmp

Container image for Podman/Docker/etc. with [**Nginx**](http://nginx.org/en/) using
the [**nginx-rtmp-module**](https://github.com/arut/nginx-rtmp-module) module for live multimedia (video) streaming.

## Description

This [**Docker**](https://www.docker.com/) image can be used to create an RTMP server for multimedia / video streaming
using [**Nginx**](http://nginx.org/en/) and [**nginx-rtmp-module**](https://github.com/arut/nginx-rtmp-module),
built from the current latest sources (Nginx 1.16.1 and nginx-rtmp-module 1.2.1).

Based on image from [tiangolo](https://github.com/tiangolo/nginx-rtmp-docker), which was inspired by similar images from
[dvdgiessen](https://hub.docker.com/r/dvdgiessen/nginx-rtmp-docker/),
[jasonrivers](https://hub.docker.com/r/jasonrivers/nginx-rtmp/),
[aevumdecessus](https://hub.docker.com/r/aevumdecessus/docker-nginx-rtmp/) and by an
[OBS Studio post](https://obsproject.com/forum/resources/how-to-set-up-your-own-private-rtmp-server-using-nginx.50/).

The main purpose (and test case) to build it was to allow streaming from [**OBS Studio**](https://obsproject.com/)
to different clients at the same time.

**GitHub repo**: <https://github.com/fenryxo/nginx-rtmp-docker>

**Docker Hub image**: <https://hub.docker.com/r/fenryxo/nginx-rtmp/>

## Details

If you don't use `podman` yet, replace `podman` commands with `docker` command.

## How to use

* For the simplest case, just run a container with this image:

```bash
podman run -d -p 1935:1935 --name nginx-rtmp fenryxo/nginx-rtmp
```

* Stop it with: `podman stop nginx-rtmp`
* Remove it with: `podman rm nginx-rtmp`

## How to test with OBS Studio and VLC

* Run a container with the command above


* Open [OBS Studio](https://obsproject.com/)
* Click the "Settings" button
* Go to the "Stream" section
* In "Stream Type" select "Custom Streaming Server"
* In the "URL" enter the `rtmp://<ip_of_host>/live` replacing `<ip_of_host>` with the IP of the host in which the container is running. For example: `rtmp://192.168.0.30/live`
* In the "Stream key" use a "key" that will be used later in the client URL to display that specific stream. For example: `test`
* Click the "OK" button
* In the section "Sources" click de "Add" button (`+`) and select a source (for example "Screen Capture") and configure it as you need
* Click the "Start Streaming" button


* Open a [VLC](http://www.videolan.org/vlc/index.html) player (it also works in Raspberry Pi using `omxplayer`)
* Click in the "Media" menu
* Click in "Open Network Stream"
* Enter the URL from above as `rtmp://<ip_of_host>/live/<key>` replacing `<ip_of_host>` with the IP of the host in which the container is running and `<key>` with the key you created in OBS Studio. For example: `rtmp://192.168.0.30/live/test`
* Click "Play"
* Now VLC should start playing whatever you are transmitting from OBS Studio

## Debugging

If something is not working you can check the logs of the container with:

```bash
podman logs nginx-rtmp
```

## Extending

If you need to modify the configurations you can create a file `nginx.conf` and replace the one in this image using a `Dockerfile` that is based on the image, for example:

```Dockerfile
FROM fenryxo/nginx-rtmp

COPY nginx.conf /etc/nginx/nginx.conf
```

The current `nginx.conf` contains:

```Nginx
worker_processes auto;
rtmp_auto_push on;
events {}
rtmp {
    server {
        listen 1935;
        listen [::]:1935 ipv6only=on;

        application live {
            live on;
            record off;
        }
    }
}
```

You can start from it and modify it as you need. Here's the [documentation related to `nginx-rtmp-module`](https://github.com/arut/nginx-rtmp-module/wiki/Directives).

## Local build

```
podman build -t fenryxo/nginx-rtmp:latest .
```

## Technical details

* This image is built from the slim Debian stable image.

* It is built from the official sources of **Nginx** and **nginx-rtmp-module** without adding anything else. (Surprisingly, most of the available images that include **nginx-rtmp-module** are made from different sources, old versions or add several other components).

* It has a simple default configuration that should allow you to send one or more streams to it and have several clients receiving multiple copies of those streams simultaneously. (It includes `rtmp_auto_push` and an automatic number of worker processes).

## License

This project is licensed under the terms of the MIT License.
