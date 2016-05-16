---
title: Automated Backups on FreeBSD
date: 2013-10-22
---
I recently had to think about a backup strategy. The FreeBSD manual <a href="http://www.freebsd.org/doc/en/books/handbook/backup-basics.html">has an entry</a> describing the method I used to use in the past:

> ### 19.10.6. Do Nothing
“Do nothing” is not a computer program, but it is the most widely used backup strategy. There are no initial costs. There is no backup schedule to follow. Just say no. If something happens to your data, grin and bear it!

> If your time and data is worth little to nothing, then “Do nothing” is the most suitable backup program for the computer. But beware, FreeBSD is a useful tool and over time it can be used to create a valuable collection of files.

To mount my backup space at Hetzner automatically, I used Samba and this fstab entry:

	//u12345@your-backup.de/backup /mnt/backup smbfs rw,late,-N,-I188.40.5.173

There is a post on the FreeBSD forums about this that I can't find anymore, but the missing space between the `-I` option and the IP address is not a typo!

The `-N` option tells Samba to not ask for a password so you need to add it to the `/etc/nsmb.conf`:

    [YOUR-BACKUP.DE:U12345]
    password=yourpassword

Both the hostname and the username must be in uppercase!

The recommended tools for doing backups are `dump` (creates backups) and `restore` (restores backups).

Dump can create full and incremental backups. Unlike rsync, it will only backup mountpoints (aka devices), not directories. If you need more flexibility, you should take a look at rsync instead.

To do incremental backups, dump uses so called <em>dump levels</em>. Here is the explanation from the manpage:

> A level 0, full backup, guarantees the entire file
system is copied (but see also the -h option below). A level number above 0, incremental backup, tells dump to copy all files new or modified since the last dump of any lower level. The default level is 0.

What this means is that dump stores a date when a backup finished and which dump level has been used and uses these to do further incremental backups. Here is how I do my automatic backups via cron:

    0 4 * * 0 /sbin/dump -0uLa -f /mnt/backup/root0 /
    0 4 * * 1 /sbin/dump -1uLa -f /mnt/backup/root1 /
    0 4 * * 2 /sbin/dump -2uLa -f /mnt/backup/root2 /
    0 4 * * 3 /sbin/dump -3uLa -f /mnt/backup/root3 /
    0 4 * * 4 /sbin/dump -4uLa -f /mnt/backup/root4 /
    0 4 * * 5 /sbin/dump -5uLa -f /mnt/backup/root5 /
    0 4 * * 6 /sbin/dump -6uLa -f /mnt/backup/root6 /

Each day of a week at 4 a.m., this will execute dump.

- On Sundays (day 0), it will do a full backup (dump level 0) of / and store it at `/mnt/backup/root0`.
- On Mondays (day 1), it will do a incremental level 1 backup including  all changes since dump level 0 and store it at `/mnt/backup/root1`.
- On Tuesdays (day 1), it will do a incremental level 2 backup including  all changes since dump level 1 and store it at `/mnt/backup/root2`.
- ...


So what you get is a full backup every sunday and backups including the daily changes every other day of the week.

About the options:

- u: Tells dump to update the dumpfile at `/etc/dumpdates` automatically. Required for incremental backups to work.
- L: Tells dump that it is working on a live file system so it will create a snapshot.
- a: [Do not ask any questions](http://lists.freebsd.org/pipermail/freebsd-questions/2007-July/153164.html).
- f: Target file.

For a complete explanation of these options, read the dump manpage.
