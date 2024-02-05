## OpenCage

We recommend using [OpenCage](https://opencagedata.com) as reverse geo-coding provider: their [pricing](https://opencagedata.com/pricing) is attractive and they currently offer a free tier which allows up to 2,500 requests per day.

After signing up and obtaining your API key, configure the OpenCage API in Recorder by

- setting `OTR_GEOKEY="opencage:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"` in the defaults file or in the environment
- invoke Recorder with the `--geokey "opencage:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"` option

Be aware that the Recorder queries the service with `no_record=1&limit=1`. OpenCage documents the first as meaning it will not log the request, and that protects your privacy.

If you configured your OpenCage API key in [quicksetup](../guide/quicksetup.md), the Recorder is appropriately configured.

(Without the substring `opencage:` the Recorder falls back to using Google in order to maintain backwards-compatibility.)
