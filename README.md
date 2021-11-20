# drakclock-patch
Mageia-7/8 has a bug (27195) that does not allow you to set and apply automatic time synchronization via `NTP` (Mageia Control Center, drakclock). The rpm package located here fixes this problem. After installation, the following commands are executed automatically (%post):
```
systemctl stop chronyd ntpd
systemctl disable chronyd ntpd

[[ -f /etc/chrony.conf ]] && sed -i 's/^pool.*/server iburst/g' /etc/chrony.conf
[[ -f /etc/ntp.conf ]] && sed -i 's/^pool.*/server iburst/g' /etc/ntp.conf
```

...and appropriate changes are made to the files: `/etc/chrony.conf` and `/etc/ntp.conf` if available.

Similarly, instead of installing this patch package, you can make changes manually:
```
su/password
systemctl stop chronyd ntpd; systemctl disable chronyd ntpd
sed -i 's/^pool.*/server iburst/g' {/etc/ntp.conf,/etc/chrony.conf}
```
**Note:** All actions must be applied to unmodified, original configuration files `/etc/ntp.conf` and `/etc/chrony.conf`.

If the files `/etc/ntp.conf` and `/etc/chrony.conf` already changed manually:
```
su/password
systemctl stop ntpd cronyd; systemctl disable ntpd cronyd
wget https://github.com/AKotov-dev/drakclock-patch/raw/main/ntp.conf_and_chrony.conf_corrected.tar.gz
#...and overwrite the necessary files from the archive
``` 

![](https://github.com/AKotov-dev/drakclock-patch/blob/main/ScreenShot.png)

# drakclock_test.tar.gz

![](https://github.com/AKotov-dev/drakclock-patch/blob/main/DrakClock_Test.png)

The program is designed for active monitoring of the state of ntpd.service and chronyd.service services and is intended for configuring the "NTP" function in drakclock (Mageia Linux x86_64). When "drakclock_test" is started, it displays the full state of ntpd and chronyd (active/inactive, enabled/disabled) IN REAL TIME. This allows you to test drakclock in various ways: delete ntp/chrony packages, manage NTP from drakclock and immediately monitor the status.
