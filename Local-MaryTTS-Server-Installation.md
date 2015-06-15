# Install MaryTTS to a local server

The following guidelines document how to install MaryTTS to a local server.

## Preliminaries

Please not that this documentation assumes the use of a Ubuntu server.
First, create a dedicated service account named `mary` to manage the installation files and run the service.
Then, place the MaryTTS installation in `/local/mary/marytts` and serve the documentation from `/local/mary/www`.
The MaryTTS server itself will run on the default port 59125. Link to this server: [MaryServer](http://localhost:59125/)

```bash
$ sudo useradd -m -r mary
$ sudo mkdir -p /local/mary
$ sudo chown mary:mary /local/mary
```

## Download the MaryTTS source code

The best way to obtain the source code is to clone it from GitHub using `git`. For this you will need a git account which you can create easily on the [github.com](https://github.com/) website. You will also need to install git.

```bash
$ sudo apt-get install -y git
$ sudo -u mary git clone https://github.com/marytts/marytts.git /local/mary/marytts
$ cd /local/mary/marytts
```

## Build MaryTTS

In oder to build Mary, JDK 7 and Maven might need to be installed first.

```bash
$ sudo apt-get install -y openjdk-7-jdk maven
$ sudo -u mary mvn package
```
## Run the MaryTTS HTTP server

We can start the MaryTTS server as a HTTP server.
```bash
$ sudo -u mary /local/mary/marytts/target/marytts-5.1-beta2/bin/marytts-server.sh
```
Of course a proper init script would be nice... [Here's one](https://github.com/marytts/marytts/blob/e8384220f9308a0b660f72df4c90ab7f88feb06d/marytts-assembly/assembly-runtime/src/runtime/doc/examples/etc_init.d_marytts) that was used on the old, retired demo server.


## Build MaryTTS website (optional)

This is not required to build a mary voice. This can be used by people who, for example, would like to have Mary on their websites.

The MaryTTS artifacts need to be installed in the local Maven repository first.

```bash
$ sudo -u mary mvn install
$ sudo -u mary mvn site:site site:stage -DstagingDirectory=/local/mary/www
```

We will use `nginx` for this and configure it to serve the MaryTTS website.
```bash
$ sudo apt-get install -y nginx
```

Add a configuration file to `/etc/nginx/sites-available` with contents like this:
```nginx
server {
  location / {
    alias /local/mary/www/;
  }
}
```
Then create a symbolic link to that file in `/etc/nginx/sites-enabled`

Finally, start `nginx`:
```bash
$ sudo service nginx start
```
