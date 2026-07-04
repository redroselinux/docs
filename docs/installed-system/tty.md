# Virtual Consoles

Unlike most distributions which create 6 TTYs, Redrose creates 8. As of alpha-0.3->0.7 they is/will be spawned using Busybox `getty`. Each TTY has its own service named `tty<num>`, for example `tty8`. 

Each service has a `finish` script, which simply runs `clear`.

TTY services do not have `log` services, because if anything goes wrong, you see it on your screen, unlike other  services as `iwd`, `dhcp`, or `dbus`.

The `tty1` service sleeps 100 milliseconds before starting so nothing prints after it starts. This is a dirty solution which will be fixed after we rewrite `redrose-runit` instead of just patching original Runit code.
