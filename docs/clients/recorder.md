## Recorder

The [OwnTracks Recorder][1] is a lightweight program for storing and accessing location data published via MQTT by the OwnTracks apps. It is a compiled program which is easily to install and operate even on low-end hardware, and it doesn't require an external database.

### Docker

We have a [Docker image](https://hub.docker.com/r/owntracks/recorder) which you can use to launch the Recorder. The image expects a volume which you mount into it into which it will write persistent data. Furthermore, you pass environment variables into the container at first launch with which TLS certificates are created with subject (and subjAltNames) according to your preference. 

This is documented [on the docker-recorder](https://github.com/owntracks/docker-recorder) repository.

### Synology

After installing Docker on your compatible Synology DiskStation, click on _Image_ and _Add_ the Recorder image from the URL `https://hub.docker.com/r/owntracks/recorder`; you do not have to specify a username/password. Select `latest` when prompted to choose a tag.

In list of images you should then see one called `owntracks/recorder:latest` as shown below.

![](jmbp-4953.png)

Then, click on _Launch_ in the same window, select a container name (or leave it as `owntracks-recorder1`) and click on _Advanced Settings_.

1. Under _Volume_, add a folder. Select a folder you created and have reserved for the recorder, and associate (mount) that folder onto the `/store` mount path.
2. Under _Port Settings_, ensure _Local Port_ `8083` is mapped to _Container Port_ `8083`; you will have to replace `Auto` by `8083`.
3. Under _Environment_, add two variables: `OTR_HOST` with the address or host name of your MQTT broker and `OTR_PORT` with its port number (typically `1883`). If you need authentication, check the [Recorder documentation](https://github.com/owntracks/recorder) for further variables you can set to configure that.

Just before running the container, you should see something like this with the settings you specified:

![](jmbp-4954.png)

When you launch the container, it will show up in the _Container_ tab:

![](jmbp-4955.png)

You should then be able to access the Recorder's Web interface at the address of your Synology DiskStation and the port 8083 you specified above.

![](jmbp-4956.png)






  [1]: https://github.com/owntracks/recorder
  [mosquitto]: http://mosquitto.org
