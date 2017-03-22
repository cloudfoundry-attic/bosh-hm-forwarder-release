# Bosh HM Forwarder Release

Bosh HM Forwarder is a companion job to `metron_agent` that sends bosh health
metrics through loggregator.

## Usage

`bosh_hm_forwarder` is to be colocated with a `metron_agent`. It reads metrics
from the host VM and forwards them (via UDP) to the `metron_agent`.

### Deployment

#### Changes to Bosh Deployment

These instructions are colocating the job with `metron_agent`.

1. Add the release to your deployment manifest.

   ```diff
   releases:
   +  - name: bosh-hm-forwarder
   +    version: latest
      - name: loggregator
        version: latest
   ```

   Then `bosh upload release` the latest [`bosh-hm-forwarder-release`](http://bosh.io/releases/github.com/cloudfoundry/bosh-hm-fowarder-release?all=1).

1. Colocate the job that has `metron_agent`.

    ```diff
    jobs:
      - name: some_job_z1
      templates:
      - name: metron_agent
        release: loggegator
    + - name: boshhmforwarder
    +   release: bosh-hm-forwarder
      instances: 1
      resource_pool: default
      networks:
        - name: default
      properties:
      # standard metron properties...
    ```

   Then `bosh deploy` this updated manifest.

1. Validate the metrics can be seen.

   Assuming you are using `bosh-hm-forwarder-release` with CF Release, you can use the
   [CF Nozzle plugin](https://github.com/cloudfoundry/firehose-plugin)

   ```bash
   cf nozzle -filter ValueMetric | grep "system.cpu.user"
   ```

#### Enable Bosh Director with OpenTSDB Plugin

The bosh director needs to be updated to enable the [OpenTSDB health
monitor](https://bosh.io/docs/hm-config.html#tsdb).

## Example manifest

The example manifest provided in `manifests/` requires that the vars generated
from loggregator are passed in.

For example:

```bash
bosh -d boshhmforwarder -e <env> deploy manifests/boshhmforwarder.yml --vars-file ./loggregator-vars.yml
```
