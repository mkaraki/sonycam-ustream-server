# Sony Camera Ustream Server

Alternative Stream server for [Ustream supported Sony's ActionCam](https://www.sony-mea.com/en/electronics/support/action-cam-fdr-x-series/fdr-x3000r/articles/00200750).

Tested on [FDR-X1000V](https://www.sony.com/electronics/support/action-cam-fdr-x-series/fdr-x1000v)

## How to use

> [!NOTE]
> If you haven't configured Ustream before,
> see [the supported camera list](https://openmemories.readthedocs.io/devices.html) to check if your camera is supported by [Sony-PMCA-RE](https://github.com/ma1co/Sony-PMCA-RE).

### Configure Wi-Fi connection

See your camera's manual to configure Wi-Fi connection.

### Configure Ustream with Sony-PMCA-RE

1. Download [steam.cfg](https://github.com/mkaraki/sonycam-ustream-server/blob/master/pmca/stream.cfg)
2. Download [PMCA Console](https://github.com/ma1co/Sony-PMCA-RE/releases/)
3. Run `pmca-console stream -w stream.cfg` (replace `pmca-console` to your downloaded binary name)

### Build DNS Resolver

Use your favorite resolver.

Override `api.ustream.tv` to your computer's IP Address.

If your router's proxy-dns supports override, you can use that.

### Configure DHCP server to advertise fake DNS server

Configure your DHCP server to advertise a DNS server that can override Ustream domain.

### Write nginx config

Please check [the sample file](https://github.com/mkaraki/sonycam-ustream-server/blob/master/nginx/rtmp_application_settings.conf) for configuration.

Here a is sample config for YouTube Live

```nginx
push rtmp://a.rtmp.youtube.com/live2/your-stream-key-here;
```

### Run nginx Container

If you are using docker on the amd64 architecture, you can use [a prebuild image](https://github.com/mkaraki/sonycam-ustream-server/pkgs/container/sonycam-ustream-server-nginx).
If not, build an image in the `nginx` directory.

You have to expose `80/tcp` and `1935/tcp`.
You also have to bind `/etc/nginx/rtmp_application_settings.conf` to your local configuration file.

### Start stream from the camera

After that, you can start streaming from your camera.
