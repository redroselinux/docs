# redrose-runit

Redrose-runit (short: runquit) is an init system based on Runit.

The only Redrose-specific changes are that we have edited the source code to be packaged as a Car package and we expect /etc/service.
Patches for functionality inside of runit:
- "Starting" logs integrated
- The sv command is improved

Patches we are planning to make:
- Let services set a display name and color
- Let services to not print "Starting" on boot
- Let services tell runquit how they should be restarted
- Service dependencies
- If runsv fails to start because of an error in the service, try chmod 755 and if that does not work do not start the service and let the user know

## Build from source

Clone the source:

```bash
git clone https://github.com/redroselinux/redrose-runit
cd redrose-runit
```

Simply run: 

```bash
make
```
