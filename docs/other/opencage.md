## OpenCageData

We've had excellent experience using [OpenCageData](https://opencagedata.com) as reverse geo-coding provider: their [pricing](https://opencagedata.com/pricing) is attractive and they currently offer a free tier that allows up to 2,500 requests per day, which should be more than sufficient for our purposes here.

After signing up and obtaining your API key, configure the OpenCageData API in Recorder by

- setting `OTR_GEOKEY="opencage:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"` in the defaults file or in the environment
- invoke Recorder with the `--geokey "opencage:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"` option

If you configured your OpenCageData API key in [quicksetup](../guide/quicksetup.md), the Recorder is appropriately configured.

Be aware that the Recorder queries the service with `no_record=1&limit=1`. OpenCageData documents the first as meaning it will not log the request, and that protects your privacy.

(Without the substring `opencage:` the Recorder falls back to using Google in order to maintain backwards-compatibility.)
