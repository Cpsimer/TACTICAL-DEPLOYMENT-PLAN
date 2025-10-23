# System Status - October 23, 2025 12:53 AM

```text
camr@schlimers-server:~$ systemctl status
● schlimers-server
    State: degraded
    Units: 683 loaded (incl. loaded aliases)
     Jobs: 0 queued
   Failed: 2 units
    Since: Thu 2025-10-23 00:35:17 EDT; 13min ago
  systemd: 257.9-0ubuntu2
  Tainted: unmerged-bin
   CGroup: /
           ├─init.scope
           │ └─1 /usr/lib/systemd/systemd --switched-root --system --deserialize=49 splash
           ├─kubepods
           │ ├─besteffort
           │ │ └─podeb98489f-2c21-4646-a8e2-5794305e499b
           │ │   ├─31a97a9f2e56771f100cc5f4cb817d1b2d1e0be5e603da8a3c20e06a8890c20a
           │ │   │ └─5725 /pause
           │ │   └─ba07312f69394ad414836fd1c68c946e44a13b951eafb994644eb69623770a72
           │ │     └─5797 /usr/bin/kube-controllers
           │ └─burstable
           │   ├─pod7a608a70-1477-4179-a6af-7a1b060a0aa6
           │   │ ├─5400b9de209cb5726209421a63bf69b5250e0d04fa81757c27bd9074e25839ee
           │   │ │ └─5815 /coredns -conf /etc/coredns/Corefile
           │   │ └─5477575b51f29cf700dbb349fd41dd9d322c628e2f64713154b49d035bddc6e1
           │   │   └─5660 /pause
           │   └─podb2088ca8-4ec3-43f7-9140-deb69a5b65a4
           │     ├─17b334238e462099307e7bebe4c015ba726c77c317130c2d19efdb2232115842
           │     │ └─5659 /pause
           │     └─48b3052f59fd3714b85fcedffbc8bf4dfac7cb77eff77ac773729492e5f1d4c1
           │       ├─6019 /usr/local/bin/runsvdir -P /etc/service/enabled
           │       ├─6092 runsv cni
           │       ├─6093 runsv node-status-reporter
           │       ├─6094 runsv monitor-addresses
           │       ├─6095 runsv allocate-tunnel-addrs
           │       ├─6096 runsv felix
           │       ├─6097 calico-node -status-reporter
           │       ├─6098 calico-node -monitor-addresses
           │       ├─6099 calico-node -monitor-token
           │       ├─6100 calico-node -allocate-tunnel-addrs
           │       └─6101 calico-node -felix
           ├─system.slice
           │ ├─ModemManager.service
           │ │ └─2134 /usr/sbin/ModemManager
           │ ├─NetworkManager.service
           │ │ └─2044 /usr/sbin/NetworkManager --no-daemon
           │ ├─accounts-daemon.service
           │ │ └─1953 /usr/libexec/accounts-daemon
           │ ├─avahi-daemon.service
           │ │ ├─1921 "avahi-daemon: running [schlimers-server.local]"
           │ │ └─2002 "avahi-daemon: chroot helper"
           │ ├─bluetooth.service
           │ │ └─1922 /usr/libexec/bluetooth/bluetoothd
           │ ├─bolt.service
           │ │ └─2135 /usr/libexec/boltd
           │ ├─chrony.service
           │ │ ├─1923 /bin/sh /usr/lib/systemd/scripts/chronyd-starter.sh -n -F 1
           │ │ ├─2042 /usr/sbin/chronyd -n -F 1
           │ │ └─2080 /usr/sbin/chronyd -n -F 1
           │ ├─colord.service
           │ │ └─3702 /usr/libexec/colord
           │ ├─containerd.service
           │ │ └─2393 /usr/bin/containerd
           │ ├─cron.service
           │ │ └─1955 /usr/sbin/cron -f -P
           │ ├─dbus.service
           │ │ ├─1925 @dbus-daemon --system --address=systemd: --nofork --nopidfile --systemd-activation --syslog-only
           │ │ └─9541 python3 /usr/lib/software-properties/software-properties-dbus
           │ ├─fwupd.service
           │ │ └─12856 /usr/libexec/fwupd/fwupd
           │ ├─gdm.service
           │ │ └─2513 /usr/sbin/gdm3
           │ ├─iperf3.service
           │ │ └─2275 /usr/bin/iperf3 --server --interval 0
           │ ├─networkd-dispatcher.service
           │ │ └─1936 /usr/bin/python3 /usr/bin/networkd-dispatcher --run-startup-triggers
           │ ├─packagekit.service
           │ │ └─21898 /usr/libexec/packagekitd
           │ ├─polkit.service
           │ │ └─1943 /usr/lib/polkit-1/polkitd --no-debug --log-level=notice
           │ ├─power-profiles-daemon.service
           │ │ └─6595 /usr/libexec/power-profiles-daemon
           │ ├─rsyslog.service
           │ │ └─2067 /usr/sbin/rsyslogd -n -iNONE
           │ ├─rtkit-daemon.service
           │ │ └─2709 /usr/libexec/rtkit-daemon
           │ ├─snap.cups.cups-browsed.service
           │ │ ├─2298 /bin/sh /snap/cups/1112/scripts/run-cups-browsed
           │ │ ├─3647 /bin/sh /snap/cups/1112/scripts/run-cups-browsed
           │ │ └─3648 sleep 3600
           │ ├─snap.cups.cupsd.service
           │ │ ├─2300 /bin/sh /snap/cups/1112/scripts/run-cupsd
           │ │ ├─3458 cupsd -f -s /var/snap/cups/common/etc/cups/cups-files.conf -c /var/snap/cups/common/etc/cups/cupsd.conf
           │ │ └─3459 cups-proxyd /var/snap/cups/common/run/cups.sock /run/cups/cups.sock -l --logdir /var/snap/cups/1112/var/log
           │ ├─snap.microk8s.daemon-apiserver-kicker.service
           │ │ ├─ 2305 bash /snap/microk8s/8492/apiservice-kicker
           │ │ └─32046 sleep 5
           │ ├─snap.microk8s.daemon-cluster-agent.service
           │ │ ├─2316 bash /snap/microk8s/8492/run-cluster-agent-with-args
           │ │ └─3219 /snap/microk8s/8492/bin/cluster-agent cluster-agent --bind 0.0.0.0:25000 --keyfile /var/snap/microk8s/8492/certs/server.key --certfile /var/snap/microk8s/8492/certs/server.crt --timeout 240
           │ ├─snap.microk8s.daemon-containerd.service
           │ │ ├─2325 /snap/microk8s/8492/bin/containerd --config /var/snap/microk8s/8492/args/containerd.toml --root /var/snap/microk8s/common/var/lib/containerd --state /var/snap/microk8s/common/run/containerd --address /var/snap/micro>
           │ │ ├─5509 /snap/microk8s/8492/bin/containerd-shim-runc-v2 -namespace k8s.io -id 17b334238e462099307e7bebe4c015ba726c77c317130c2d19efdb2232115842 -address /var/snap/microk8s/common/run/containerd.sock
           │ │ ├─5622 /snap/microk8s/8492/bin/containerd-shim-runc-v2 -namespace k8s.io -id 5477575b51f29cf700dbb349fd41dd9d322c628e2f64713154b49d035bddc6e1 -address /var/snap/microk8s/common/run/containerd.sock
           │ │ └─5698 /snap/microk8s/8492/bin/containerd-shim-runc-v2 -namespace k8s.io -id 31a97a9f2e56771f100cc5f4cb817d1b2d1e0be5e603da8a3c20e06a8890c20a -address /var/snap/microk8s/common/run/containerd.sock
           │ ├─snap.microk8s.daemon-k8s-dqlite.service
           │ │ └─2342 /snap/microk8s/8492/bin/k8s-dqlite --storage-dir=/var/snap/microk8s/8492/var/kubernetes/backend/ --listen=unix:///var/snap/microk8s/8492/var/kubernetes/backend/kine.sock:12379
           │ ├─snap.microk8s.daemon-kubelite.service
           │ │ └─4730 /snap/microk8s/8492/kubelite --scheduler-args-file=/var/snap/microk8s/8492/args/kube-scheduler --controller-manager-args-file=/var/snap/microk8s/8492/args/kube-controller-manager --proxy-args-file=/var/snap/microk8s>
           │ ├─snapd.service
           │ │ └─1952 /snap/snapd/current/usr/lib/snapd/snapd
           │ ├─switcheroo-control.service
           │ │ └─1957 /usr/libexec/switcheroo-control
           │ ├─system-cups.slice
           │ │ ├─cups-browsed.service
           │ │ │ └─4434 /usr/sbin/cups-browsed
           │ │ └─cups.service
           │ │   ├─2274 /usr/sbin/cupsd -l
           │ │   ├─2385 /usr/lib/cups/notifier/dbus dbus://
           │ │   ├─2386 /usr/lib/cups/notifier/dbus dbus://
           │ │   ├─2387 /usr/lib/cups/notifier/dbus dbus://
           │ │   ├─2389 /usr/lib/cups/notifier/dbus dbus://
           │ │   └─2390 /usr/lib/cups/notifier/dbus dbus://
           │ ├─systemd-journald.service
           │ │ └─645 /usr/lib/systemd/systemd-journald
           │ ├─systemd-logind.service
           │ │ └─1961 /usr/lib/systemd/systemd-logind
           │ ├─systemd-oomd.service
           │ │ └─690 /usr/lib/systemd/systemd-oomd
           │ ├─systemd-resolved.service
           │ │ └─691 /usr/lib/systemd/systemd-resolved
           │ ├─systemd-udevd.service
           │ │ └─udev
           │ │   └─696 /usr/lib/systemd/systemd-udevd
           │ ├─udisks2.service
           │ │ └─1966 /usr/libexec/udisks2/udisksd
           │ ├─unattended-upgrades.service
           │ │ └─2357 /usr/bin/python3 /usr/share/unattended-upgrades/unattended-upgrade-shutdown --wait-for-signal
           │ ├─upower.service
           │ │ └─3754 /usr/libexec/upowerd
           │ └─wpa_supplicant.service
           │   └─2045 /usr/sbin/wpa_supplicant -u -s -O "DIR=/run/wpa_supplicant GROUP=netdev"
           └─user.slice
             └─user-1000.slice
               ├─session-2.scope
               │ ├─2557 "gdm-session-worker [pam/gdm-autologin]"
               │ ├─2717 /usr/libexec/gdm-wayland-session "env GNOME_SHELL_SESSION_MODE=ubuntu /usr/bin/gnome-session --session=ubuntu"
               │ └─2729 /usr/libexec/gnome-session-init-worker ubuntu
               └─user@1000.service
                 ├─app.slice
                 │ ├─app-gnome-gigabyte\x2dai\x2dtop\x2dutility-6694.scope
                 │ │ ├─6694 "/opt/gigabyte-ai-top-utility/gigabyte-ai-top-utility --no-sandbox"
                 │ │ ├─6697 "/opt/gigabyte-ai-top-utility/gigabyte-ai-top-utility --type=zygote --no-zygote-sandbox --no-sandbox"
                 │ │ ├─6698 "/opt/gigabyte-ai-top-utility/gigabyte-ai-top-utility --type=zygote --no-sandbox"
                 │ │ ├─6738 "/opt/gigabyte-ai-top-utility/gigabyte-ai-top-utility --type=gpu-process --no-sandbox --enable-crash-reporter=5dba0ca7-e955-46ca-9988-eecf7fc6d0af,no_channel --user-data-dir=/home/camr/.config/gigabyte-ai-top->
                 │ │ └─6743 "/opt/gigabyte-ai-top-utility/gigabyte-ai-top-utility --type=utility --utility-sub-type=network.mojom.NetworkService --lang=en-US --service-sandbox-type=none --no-sandbox --enable-crash-reporter=5dba0ca7-e955->
                 │ ├─app-gnome-org.gnome.Evolution\x2dalarm\x2dnotify-3834.scope
                 │ │ └─3834 /usr/libexec/evolution-data-server/evolution-alarm-notify
                 │ ├─app-gnome-org.gnome.SettingsDaemon.DiskUtilityNotify-3844.scope
                 │ │ └─3844 /usr/libexec/gsd-disk-utility-notify
                 │ ├─app-gnome-update\x2dnotifier-3829.scope
                 │ │ └─3829 /usr/bin/update-notifier
                 │ ├─app-gnome-xdg\x2dterminal\x2dexec-5006.scope
                 │ │ ├─5006 ptyxis --new-window
                 │ │ └─5023 /usr/libexec/ptyxis-agent --socket-fd=3 --rlimit-nofile=1024
                 │ ├─app-gnome\x2dsession\x2dmanager.slice
                 │ │ └─gnome-session-manager@ubuntu.service
                 │ │   └─2864 /usr/libexec/gnome-session-service --session=ubuntu
                 │ ├─dconf.service
                 │ │ └─4209 /usr/libexec/dconf-service
                 │ ├─docker.service
                 │ │ ├─2637 rootlesskit --state-dir=/run/user/1000/dockerd-rootless --net=slirp4netns --mtu=65520 --slirp4netns-sandbox=auto --slirp4netns-seccomp=auto --disable-host-loopback --port-driver=builtin --copy-up=/etc --copy-u>
                 │ │ ├─2719 /proc/self/exe --state-dir=/run/user/1000/dockerd-rootless --net=slirp4netns --mtu=65520 --slirp4netns-sandbox=auto --slirp4netns-seccomp=auto --disable-host-loopback --port-driver=builtin --copy-up=/etc --cop>
                 │ │ ├─2779 slirp4netns --mtu 65520 -r 3 --disable-host-loopback --enable-sandbox --enable-seccomp 2719 tap0
                 │ │ ├─2798 dockerd
                 │ │ └─2900 containerd --config /run/user/1000/docker/containerd/containerd.toml
                 │ ├─evolution-addressbook-factory.service
                 │ │ └─4105 /usr/libexec/evolution-addressbook-factory
                 │ ├─evolution-calendar-factory.service
                 │ │ └─4067 /usr/libexec/evolution-calendar-factory
                 │ ├─evolution-source-registry.service
                 │ │ └─3746 /usr/libexec/evolution-source-registry
                 │ ├─gcr-ssh-agent.service
                 │ │ └─2834 /usr/libexec/gcr-ssh-agent --base-dir /run/user/1000/gcr
                 │ ├─gnome-keyring-daemon.service
                 │ │ └─2642 /usr/bin/gnome-keyring-daemon --foreground --components=pkcs11,secrets --control-directory=/run/user/1000/keyring
                 │ ├─gnome-session-monitor.service
                 │ │ └─2835 /usr/libexec/gnome-session-ctl --monitor
                 │ ├─mpris-proxy.service
                 │ │ └─2707 /usr/bin/mpris-proxy
                 │ ├─obex.service
                 │ │ └─10125 /usr/libexec/bluetooth/obexd
                 │ ├─ptyxis-spawn-c01ea590-51cf-4bb2-bc10-7e3687186569.scope
                 │ │ ├─ 5111 /usr/bin/bash
                 │ │ ├─32099 systemctl status
                 │ │ └─32100 pager
                 │ ├─rygel.service
                 │ │ └─12110 /usr/bin/rygel
                 │ ├─snap.obsidian.obsidian-3d212ad8-f105-4a9a-a0b4-adc29efe84ac.scope
                 │ │ ├─18991 /snap/obsidian/50/obsidian --no-sandbox
                 │ │ ├─19054 /snap/obsidian/50/obsidian --type=zygote --no-zygote-sandbox --no-sandbox
                 │ │ ├─19055 /snap/obsidian/50/obsidian --type=zygote --no-sandbox
                 │ │ ├─19103 /snap/obsidian/50/obsidian --type=zygote --no-zygote-sandbox --no-sandbox
                 │ │ ├─19126 /proc/self/exe --type=utility --utility-sub-type=network.mojom.NetworkService --lang=en-US --service-sandbox-type=none --no-sandbox --enable-crash-reporter=89e57bf6-97e8-4c06-99cc-487db143cdce,no_channel --us>
                 │ │ └─19162 /proc/self/exe --type=renderer --enable-crash-reporter=89e57bf6-97e8-4c06-99cc-487db143cdce,no_channel --user-data-dir=/home/camr/snap/obsidian/50/.config/obsidian --standard-schemes=app --secure-schemes=app >
                 │ ├─snap.snap-store.snap-store-1f38d687-9324-412d-a68c-1c510ea59ead.scope
                 │ │ └─8265 /snap/snap-store/1270/bin/snap-store
                 │ ├─ssh-agent.service
                 │ │ └─2837 /usr/bin/ssh-agent -D
                 │ ├─ubuntu-insights-upload.service
                 │ │ └─20967 /usr/bin/ubuntu-insights upload -r
                 │ ├─xdg-desktop-portal-gnome.service
                 │ │ └─4245 /usr/libexec/xdg-desktop-portal-gnome
                 │ └─xdg-desktop-portal-gtk.service
                 │   └─4295 /usr/libexec/xdg-desktop-portal-gtk
                 ├─background.slice
                 │ └─localsearch-3.service
                 │   └─4054 /usr/libexec/localsearch-3
                 ├─init.scope
                 │ ├─2399 /usr/lib/systemd/systemd --user
                 │ └─2465 "(sd-pam)"
                 └─session.slice
                   ├─at-spi-dbus-bus.service
                   │ ├─3692 /usr/libexec/at-spi-bus-launcher
                   │ ├─3699 /usr/bin/dbus-daemon --config-file=/usr/share/defaults/at-spi2/accessibility.conf --nofork --print-address 11 --address=unix:path=/run/user/1000/at-spi/bus
                   │ └─3701 /usr/libexec/at-spi2-registryd --use-gnome-session
                   ├─dbus.service
                   │ ├─ 2636 /usr/bin/dbus-daemon --session --address=systemd: --nofork --nopidfile --systemd-activation --syslog-only
                   │ ├─ 3735 /usr/libexec/gnome-shell-calendar-server
                   │ ├─ 3752 /usr/bin/gjs -m /usr/share/gnome-shell/org.gnome.Shell.Notifications
                   │ ├─ 3969 /usr/bin/gjs -m /usr/share/gnome-shell/org.gnome.ScreenSaver
                   │ ├─ 3998 /usr/libexec/ibus-portal
                   │ ├─ 4041 /usr/libexec/goa-daemon
                   │ ├─ 4073 /usr/libexec/goa-identity-service
                   │ ├─ 8488 /snap/snapd/current/usr/bin/snap userd
                   │ └─16081 /usr/bin/gnome-control-center --gapplication-service
                   ├─filter-chain.service
                   │ └─2640 /usr/bin/pipewire -c filter-chain.conf
                   ├─gvfs-afc-volume-monitor.service
                   │ └─4090 /usr/libexec/gvfs-afc-volume-monitor
                   ├─gvfs-daemon.service
                   │ ├─2852 /usr/libexec/gvfsd
                   │ ├─2862 /usr/libexec/gvfsd-fuse /run/user/1000/gvfs -f
                   │ ├─4146 /usr/libexec/gvfsd-mtp --spawner :1.16 /org/gtk/gvfs/exec_spaw/0
                   │ └─4189 /usr/libexec/gvfsd-trash --spawner :1.16 /org/gtk/gvfs/exec_spaw/1
                   ├─gvfs-goa-volume-monitor.service
                   │ └─4082 /usr/libexec/gvfs-goa-volume-monitor
                   ├─gvfs-gphoto2-volume-monitor.service
                   │ └─4111 /usr/libexec/gvfs-gphoto2-volume-monitor
                   ├─gvfs-metadata.service
                   │ └─4328 /usr/libexec/gvfsd-metadata
                   ├─gvfs-mtp-volume-monitor.service
                   │ └─4098 /usr/libexec/gvfs-mtp-volume-monitor
                   ├─gvfs-udisks2-volume-monitor.service
                   │ └─4052 /usr/libexec/gvfs-udisks2-volume-monitor
                   ├─org.freedesktop.IBus.session.GNOME.service
                   │ ├─3774 /usr/bin/ibus-daemon --panel disable
                   │ ├─3981 /usr/libexec/ibus-dconf
                   │ ├─3992 /usr/libexec/ibus-extension-gtk3
                   │ └─4145 /usr/libexec/ibus-engine-simple
                   ├─org.gnome.SettingsDaemon.A11ySettings.service
                   │ └─3781 /usr/libexec/gsd-a11y-settings
                   ├─org.gnome.SettingsDaemon.Color.service
                   │ └─3782 /usr/libexec/gsd-color
                   ├─org.gnome.SettingsDaemon.Datetime.service
                   │ └─3784 /usr/libexec/gsd-datetime
                   ├─org.gnome.SettingsDaemon.Housekeeping.service
                   │ └─3788 /usr/libexec/gsd-housekeeping
                   ├─org.gnome.SettingsDaemon.Keyboard.service
                   │ └─3790 /usr/libexec/gsd-keyboard
                   ├─org.gnome.SettingsDaemon.MediaKeys.service
                   │ └─3795 /usr/libexec/gsd-media-keys
                   ├─org.gnome.SettingsDaemon.Power.service
                   │ └─3798 /usr/libexec/gsd-power
                   ├─org.gnome.SettingsDaemon.PrintNotifications.service
                   │ ├─3799 /usr/libexec/gsd-print-notifications
                   │ └─4288 /usr/libexec/gsd-printer
                   ├─org.gnome.SettingsDaemon.Rfkill.service
                   │ └─3802 /usr/libexec/gsd-rfkill
                   ├─org.gnome.SettingsDaemon.ScreensaverProxy.service
                   │ └─3805 /usr/libexec/gsd-screensaver-proxy
                   ├─org.gnome.SettingsDaemon.Sharing.service
                   │ └─3822 /usr/libexec/gsd-sharing
                   ├─org.gnome.SettingsDaemon.Smartcard.service
                   │ └─3830 /usr/libexec/gsd-smartcard
                   ├─org.gnome.SettingsDaemon.Sound.service
                   │ └─3833 /usr/libexec/gsd-sound
                   ├─org.gnome.SettingsDaemon.UsbProtection.service
                   │ └─3846 /usr/libexec/gsd-usb-protection
                   ├─org.gnome.SettingsDaemon.Wwan.service
                   │ └─3856 /usr/libexec/gsd-wwan
                   ├─org.gnome.SettingsDaemon.XSettings.service
                   │ ├─5052 /usr/libexec/gsd-xsettings
                   │ └─5076 /usr/libexec/ibus-x11
                   ├─org.gnome.Shell@wayland.service
                   │ ├─2892 /usr/bin/gnome-shell
                   │ ├─3926 bwrap --unshare-all --die-with-parent --chdir / --ro-bind /usr /usr --dev /dev --ro-bind-try /etc/ld.so.cache /etc/ld.so.cache --ro-bind-try /nix/store /nix/store --tmpfs /tmp-home --tmpfs /tmp-run --clearenv >
                   │ ├─3936 bwrap --unshare-all --die-with-parent --chdir / --ro-bind /usr /usr --dev /dev --ro-bind-try /etc/ld.so.cache /etc/ld.so.cache --ro-bind-try /nix/store /nix/store --tmpfs /tmp-home --tmpfs /tmp-run --clearenv >
                   │ ├─3946 /usr/libexec/glycin-loaders/2+/glycin-image-rs --dbus-fd 80
                   │ ├─4309 gjs /usr/share/gnome-shell/extensions/ding@rastersoft.com/app/ding.js -E -P /usr/share/gnome-shell/extensions/ding@rastersoft.com/app
                   │ ├─5036 /usr/bin/Xwayland :0 -rootless -noreset -accessx -core -auth /run/user/1000/.mutter-Xwaylandauth.9N6UE3 -listenfd 4 -listenfd 5 -displayfd 6 -initfd 7 -byteswappedclients
                   │ └─5058 /usr/libexec/mutter-x11-frames
                   ├─pipewire-pulse.service
                   │ └─2710 /usr/bin/pipewire-pulse
                   ├─pipewire.service
                   │ └─2639 /usr/bin/pipewire
                   ├─wireplumber.service
                   │ └─2708 /usr/bin/wireplumber
                   ├─xdg-desktop-portal.service
                   │ └─4224 /usr/libexec/xdg-desktop-portal
                   ├─xdg-document-portal.service
                   │ ├─4235 /usr/libexec/xdg-document-portal
                   │ └─4241 fusermount3 -o rw,nosuid,nodev,fsname=portal,auto_unmount,subtype=portal -- /run/user/1000/doc
                   └─xdg-permission-store.service
                     └─3736 /usr/libexec/xdg-permission-store
