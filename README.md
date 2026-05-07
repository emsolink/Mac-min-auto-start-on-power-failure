# Mac-min-auto-start-on-power-failure
Making a Mac Mini power up when power is restored: Debian 11 version

Since I am running it as a server, I’d like it to reboot automatically if there is a power outage. Well, when the power outage is over and power is restored. It’s behind a UPS, so hopefully that should offer a measure of protection against mains blips anyway.

But still.

It appears – thanks, ant! – that a small change to the Mini’s power settings will make this happen:

setpci -s 0:1f.0 0xa4.b=0

(There is a seemingly credible explanation.)

However, it is not just a case of setting it once, but setting it on every (re)boot.

Most of the guides – now dated – suggest putting this in /etc/rc.local. As this has been / is being deprecated, that didn’t seem like a good idea, so I went for a systemd approach instead.

I created a .service file:

vim /etc/systemd/system/rebootonpowerrestoration.service

with content:

[Unit]
Description=Enable reboot on power restoration
    
[Service]
Type=oneshot
ExecStart=/usr/bin/setpci -s 0:1f.0 0xa4.b=0
    
[Install]
WantedBy=multi-user.target
I enabled it:

systemctl enable --now rebootonpowerrestoration.service

(The use of the the --now switch means that, as well as it being enabled for next boot, it is started/run now too.)

Did it work?
Yes. It booted fine once power was re-applied.

When I’d logged in, I checked

systemctl status rebootonpowerrestoration.service

and the log showed that the script had run.

Excellent!
