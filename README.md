# drakclock-patch
Mageia-7/8 has a bug (27195) that does not allow you to set and apply automatic time synchronization via `NTP` (Mageia Control Center, drakclock). The rpm package located here fixes this problem. After installation, the following commands are executed automatically (%post):

![](https://github.com/AKotov-dev/drakclock-patch/blob/main/ScreenShot.png)

```
systemctl stop chronyd ntpd; systemctl disable chronyd ntpd
cd /usr/libexec; patch -Ni drakclock.patch

if [[ -f /etc/chrony.conf ]]; then
cat > /etc/chrony.conf << EOF
# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (https://www.pool.ntp.org/join.html).
server iburst

# Record the rate at which the system clock gains/losses time.
driftfile /var/lib/chrony/drift

# Allow the system clock to be stepped in the first three updates
# if its offset is larger than 1 second.
makestep 1.0 3

# Enable kernel synchronization of the real-time clock (RTC).
rtcsync

# Enable hardware timestamping on all interfaces that support it.
#hwtimestamp *

# Increase the minimum number of selectable sources required to adjust
# the system clock.
#minsources 2

# Allow NTP client access from local network.
#allow 192.168.0.0/16

# Serve time even if not synchronized to a time source.
#local stratum 10

# Require authentication (nts or key option) for all NTP sources.
#authselectmode require

# Specify file containing keys for NTP authentication.
#keyfile /etc/chrony.keys

# Save NTS keys and cookies.
ntsdumpdir /var/lib/chrony

# Insert/delete leap seconds by slewing instead of stepping.
#leapsecmode slew

# Get TAI-UTC offset and leap seconds from the system tz database.
#leapsectz right/UTC

# Specify directory for log files.
logdir /var/log/chrony

# Select which information is logged.
#log measurements statistics tracking
EOF
fi;

if [[ -f /etc/ntp.conf ]]; then
cat > /etc/ntp.conf << EOF
# For more information about this file, see the ntp.conf(5) man page.

# Record the frequency of the system clock.
driftfile /var/lib/ntp/drift

# Permit time synchronization with our time source, but do not
# permit the source to query or modify the service on this system.
restrict default nomodify notrap nopeer noepeer noquery

# Permit association with pool servers.
restrict source nomodify notrap noquery

# Permit all access over the loopback interface.  This could
# be tightened as well, but to do so would effect some of
# the administrative functions.
restrict 127.0.0.1
restrict ::1

# Hosts on local network are less restricted.
#restrict 192.168.1.0 mask 255.255.255.0 nomodify notrap

# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
server iburst

# Reduce the maximum number of servers used from the pool.
tos maxclock 5

# Enable public key cryptography.
#crypto

includefile /etc/ntp/crypto/pw

# Key file containing the keys and key identifiers used when operating
# with symmetric key cryptography.
keys /etc/ntp/keys

# Specify the key identifiers which are trusted.
#trustedkey 4 8 42

# Specify the key identifier to use with the ntpdc utility.
#requestkey 8

# Specify the key identifier to use with the ntpq utility.
#controlkey 8

# Enable writing of statistics records.
#statistics clockstats cryptostats loopstats peerstats
EOF
fi;
```

# drakclock_test.tar.gz

![](https://github.com/AKotov-dev/drakclock-patch/blob/main/DrakClock_Test.png)

The program is designed for active monitoring of the state of `ntpd.service` and `chronyd.service` services and is intended for configuring the "NTP" function in drakclock (Mageia Linux x86_64). When `drakclock_test` is started, it displays the full state of `ntpd` and `chronyd` (active/inactive, enabled/disabled) IN REAL TIME. This allows you to test `drakclock` in various ways: install/delete ntp/chrony packages, manage NTP from `drakclock` and immediately monitor the status.
