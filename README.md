# ZFS to Amazon Glacier

Need a scheme to backup files long-term to Amazon Glacier. The steps and scripts used to make up this collection is an amalgemation of information from a number of different sources.

## Archive Methodology

In general there are three steps to archiving a ZFS dataset to Amazon Glacier. These tools are designed to help with 2 and 3.

  1. [Take a snapshot of the ZFS dataset][4]
  2. Create a dump of that ZFS dataset snapshot
  3. Upload the dump to Amazon Glacier

Since Amazon Glacier, and most other backup services, charge for the amount of data that is archived it is assumed that compression should be employed. Also, it assumed that the backup system you are going to employ may not be properly secured. As a result the data *MUST* be encrypted before sending over the wire to the 3rd party network. Because of these requirements I decided to benchmark different ways of encrypting/compressing data before sending to a backup client. My two optimizing parameters were time and resulting file-size. I used the `experiment` script to test real dataset snapshots to see which order of operations would lead to the smallest file in the shortest amount of time.


```
[mysql] Plain
real 0.24
user 0.00
sys 0.08
[mysql] Bytes: 45M

[mysql.bz2] AES > Bzip2
real 12.56
user 12.89
sys 0.20
[mysql.bz2] Bytes: 46M

[mysql.pbz2] AES > PBzip2
real 4.70
user 14.86
sys 0.38
[mysql.pbz2] Bytes: 46M

[mysql.bz2.aes.bz2] Bzip2 > AES > Bzip2
real 6.19
user 6.47
sys 0.12
[mysql.bz2.aes.bz2] Bytes: 5.0M

[mysql.pbz2.aes.bz2] PBzip2 > AES > PBzip2
real 2.63
user 5.88
sys 0.24
[mysql.pbz2.aes.bz2] Bytes: 5.1M
```

I ran the tests on two datasets. The first was 45MB and the second 16GB. After running the experiments, it became clear that first compressing the dataset, then encrypting the dataset, and compressing that result 1 final time resulted in both the smallest file and shortest execution time. The lessons learned by this experiment have been applied to the encrypt/decrypt scripts in this toolset.

## Retrieval Methodology


## Install

### Dependencies

  * [Parallel bzip2 implementation][7] `apt-get install pbzip2`

```
```

# Make Encryption key

`mkkey key`

# Encrypt a ZFS snapshot

`zfs-encrypt key stream/mysql@zfs-auto-snap_frequent-2013-12-19-1545 > mysql@2013-12-19-1545.bz2.aes.bz2`

### Resources

  * [Simple Encryption.md][1]
  * [Fun with ZFS send and receive][2]
  * [Moving ZFS filesystems between pools][3]
  * [zfsonlinux/zfs-auto-snapshot][4]
  * [Creating Long-Term Backups with Amazon Glacier on Linux][5]
  * [uskudnik / amazon-glacier-cmd-interface][6]

[1]: https://gist.github.com/sstephenson/5368148
[2]: http://128bitstudios.com/2010/07/23/fun-with-zfs-send-and-receive/
[3]: http://www.hlynes.com/2007/07/09/moving-zfs-filesystems-between-pools/
[4]: https://github.com/zfsonlinux/zfs-auto-snapshot
[5]: http://blog.tkassembled.com/326/creating-long-term-backups-with-amazon-glacier-on-linux/
[6]: https://github.com/uskudnik/amazon-glacier-cmd-interface/
[7]: http://compression.ca/pbzip2/