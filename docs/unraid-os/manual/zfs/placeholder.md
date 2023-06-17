# About ZFS

Read about ZFS here: For a good overview of ZFS, see this article.

ZFS pool calculator here for calculating the various setups https://www.raidz-calculator.com

# ZFS array single disks

Just like XFS. ZFS can be used in single disks in the array. These drives will function the same way the array does currently. Ie you can add any size drive at any time. 

A few notes: Single ZFS drives will have read only protection but not be self healing. To have complete self healing ZFS drives you need a pool with at least 2 drives. Single ZFS drives in the array will have no read or write performace gains. For this you need a ZFS Pool with atleast 2 drives.

# ZFS pools

See spaceinavderone's video here for How to Reformat (ZFS) and or Upgrade a Cache Pool in Unraid https://www.youtube.com/watch?v=vXF8au5o9Tw

New in this release (6.12.0) is the ability to create a ZFS file system in a user-defined pool. You may also format any data device in the unRAID array with a single-device ZFS file system. For a good overview of ZFS, see this article.

We are splitting full ZFS implementation across two Unraid OS releases. Initial support in this release includes:

Support raid0, mirror, raidz1, raidz2 and raidz3 root profiles. Up to 4-way mirror in a mirror vdev. Multiple vdev groups.
Support removing single device: if device is still present in server, 'wipefs' is used to clear the partition table.
Support replacing single missing device with a new device of same or larger size.
Support scheduled trimming of ZFS pools.
Support pool rename.

Pool names must begin with a lowercase letter and only contain lowercase letters, digits, the underscore, and dash. Pool names must not end with a digit.

Non-root vdev cannot be configured in this release, however, they can be imported. Note: imported hybrid pools may not be expanded in this release.

Pools created on other systems may or may not import depending on how the pool was created. A future update will permit importing pools from any system.

# A ZFS pool has three variables:

profile - the root data organization: raid0, mirror (up to 4-way), raidz1, raidz2, raidz3
width - the number of devices per root vdev
groups - the number of root vdevs in the pool
At time of ZFS pool creation, the webGUI will present all topology options based on the number of devices assigned to the pool.

# Special treatment for root single-vdev mirrors:

A single-device ZFS pool can be converted to multiple-device mirror by adding up to 3 additional devices in one operation.
A 2-device mirror can be increased to 3-device by adding a single device; similarly, a 3-device mirror can be increased to 4-device mirror by adding a single device.

To add an additional root vdev, you must assign 'width' number of new devices to the pool at the same time. The new vdev will be created with the same 'profile' as the existing vdevs. Additional flexibility in adding/expanding vdevs will be provided in a future update.

# Pools created with the steini84 plugin: 

These pools can be imported as follows: First create a new pool with the number of slots corresponding to the number of devices in the pool to be imported. Next assign all the devices to the new pool. Upon array Start the pool should be recognized, though certain zpool topologies may not be recognized (please report).

# ZFS Notes

Mixed topologies are not supported. For example, a pool with both a mirror root vdev and a raidz root vdev is not recognized.

Autotrim can be configured as on or off (except for single-device ZFS volumes in the unRAID array).

Compression can be configured as on or off, where on selects lz4. Future update will permit specifying other algorithms/levels.

When creating a new ZFS pool you may choose zfs - encrypted, which, like other encrypted volumes, applies device-level encryption via LUKS. ZFS native encryption is not supported at this time.

Top-level user shares in a ZFS pool are created as datasets instead of ordinary directories.

# ARC

During system boot, the file /etc/modprobe.d/zfs.conf is auto-generated to limit the ZFS ARC to 1/8 of installed memory. This can be overridden if necessary by creating a custom 'config/modprobe.d/zfs.conf' file. Future update will include ability to configure the ARC via webGUI, including auto-adjust according to memory pressure, e.g., VM start/stop.

# 'config/modprobe.d/zfs.conf' example file.

# Setting up ZFS ARC size on Unraid as per our needs
# Server has 128GB. Use this byte calculator https://convertermaniacs.com/gigabyte-to-byte/convert-16-gb-to-bytes.html 
# Set Max ARC size 1/2 of 128GB => 64GB == 64000000000 Bytes

options zfs zfs_arc_max=64000000000
 
# Set Min ARC size 1/8 of 128GB => 16GB == 16000000000 Bytes

options zfs zfs_arc_min=16000000000

