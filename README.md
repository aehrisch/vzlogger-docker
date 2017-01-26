
# vzlogger docker image for Raspberry Pi

[vzlogger](https://github.com/volkszaehler/vzlogger) is a software
that reads measurements from a variety of smart meters. This is docker
image builds `vzlogger` from its sources and runs it. The image
inherits from `armhf/debian:stable` and therefore runs on a Raspberry
Pi 3.

The image expects the configuration for `vzlogger` in
`/etc/vzlogger.conf`. Use the `-v` option to run the image with your
own configuration file.

Run the container with `--privileged` if you vzlogger to access
infrared readers connected as USB serial devices.

## Running vzlogger on startup with systemd

Create the file `/etc/systemd/system/vzlogger.service` with the
following content:

    [Unit]
    Description=Run vzlogger docker container
    After=vzgw.service

    [Service]
    Restart=always
    User=root
    Type=simple
    ExecStartPre=/bin/bash -c "/usr/bin/docker rm -f vzlogger || true"
    ExecStop=/bin/bash -c "/usr/bin/docker stop vzlogger || true"
    ExecStart=/bin/bash -c "/usr/bin/docker run --privileged --name vzlogger -v /home/pi/vzlogger.conf:/etc/vzlogger.conf:ro vzlogger:latest"

    [Install]
    WantedBy=multi-user.target

Reload `systemd` and enable the unit.
