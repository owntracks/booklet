## Recorder

The [OwnTracks Recorder][1] is a lightweight program for storing and accessing location data published via MQTT by the OwnTracks apps. It is a compiled program which is easily to install and operate even on low-end hardware, and it doesn't require an external database.

### Docker

We have a [Docker image](https://hub.docker.com/r/owntracks/recorder) which you can use to launch the Recorder. The image expects a volume which you mount into it into which it will write persistent data. Furthermore, you pass environment variables into the container at first launch with which TLS certificates are created with subject (and subjAltNames) according to your preference. 

This is documented [on the docker-recorder](https://github.com/owntracks/docker-recorder) repository.


  [1]: https://github.com/owntracks/recorder
  [mosquitto]: http://mosquitto.org
