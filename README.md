Bosh HM Forwarder Release
=========================

### Deploying Bosh HM Forwarder

```
bosh -e <env> -d boshhmforwarder deploy templates/boshhmforwarder.yml
--vars-store=<vars-file>
```

##### Note
The `<vars-file>` must match the vars from the deployment that contains the loggregator
system.
