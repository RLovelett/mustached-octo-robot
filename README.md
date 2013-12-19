# ZFS to Amazon Glacier

Need a scheme to backup files long-term to Amazon Glacier. The steps and scripts used to make up this collection is an amalgemation of information from a number of different sources.

[1] https://gist.github.com/sstephenson/5368148
[2] http://128bitstudios.com/2010/07/23/fun-with-zfs-send-and-receive

## Install

```
```

# Make Encryption key

`mkkey key`

# Encrypt a ZFS snapshot

`zfs-encrypt key stream/mysql@zfs-auto-snap_frequent-2013-12-19-1545 > mysql@2013-12-19-1545.bz2.aes.bz2`