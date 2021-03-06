---
description: How to best return data back from tape in the $ARCHIVE file system
---

# Staging data from tape in $ARCHIVE

$ARCHIVE is a special kind of file system that uses hierarchical storage management (HSM) to allow for higher utilization of storage than the physical spinning disk capacity would normally allow. HSM copies data to tapes in a magnetic tape library for long term storage; this process erases the data off of the disk capacity. In order to access your data again, it must first be "staged" back from tape, i.e. copied from tape to disk for processes to make use of it. Processes can be anything from scripts, development environments / editors, and data manipulation tools like `rsync` or `cp`.

{% hint style="info" %}
The act of moving a file or directory (mv) within the $ARCHIVE file system is an immediate operation and does not require staging the data back from tape.
{% endhint %}

## Confirm a file is offline in the $ARCHIVE file system

Before attempting to access your files on $ARCHIVE, verify that the file is actively “online”.  A file is “online” if it resides on hard disk capacity, and it is “offline” if it is solely on tape. To check if the file is currently "offline", run the following command on your file:

```
$ sls -D <path_to_file>
```

For example, if the file is in the present working directory, you would run the following for a file called test.txt:

```
$ sls -D test.txt
```

The output from that command will look something like this:

```
/import/archive/EXAMPLE/user/test.txt:
  mode: -rwxr-s---  links:   1  owner: user  group: example   
  length: 10774625242  inode:   823419671.1
  offline;
  copy 2: ------ Oct  6  2019    25d246a.392078b m2 601256
  copy 3: ------ Oct  6  2019    25d246a.392078b m2 601257
  access:        Oct  6  2019  modification: Oct  6  2019
  changed:       Oct  6  2019  attributes:   Oct  6  2019
  creation:      Sep  30 2019  residence:    Oct  8  2019
```

You can see in the output above, a lot of metadata about your file is returned, but the most important section for this document is the line stating "offline". That indicates that the file needs to be "staged" back from tape.

{% hint style="info" %}
If the text **"offline"** does not appear in the metadata output, this means that the file is currently on disk ("online").
{% endhint %}

## Batch Stage Files

To bring back one or more files from tape, use the command `batch_stage`. This command looks at the file(s) requested and orders them to be efficiently returned from tape. If no files need to be "staged" back from tape, nothing will happen and the command will return immediately.

{% hint style="info" %}
Always use **batch_stage** to return files back from tape to prevent very slow load times from tape!
{% endhint %}

Below are examples of using batch_stage:

1. Stage all files in a directory using absolute paths
     ```
     $ batch_stage $ARCHIVE/data/*
     ```

2. Stage all files in a directory recursively using absolute paths
     ```
     $ find $ARCHIVE/data -type f | batch_stage -i
     ```

3. Stage all files in a directory using relative paths (Option 1)

     ```
     $ cd $ARCHIVE/data
     $ batch_stage *
     ```

4. Stage all files in a directory using relative paths (Option 2)

     ```
     $ cd $ARCHIVE/data
     $ ls -1 | batch_stage -i
     ```

5. Stage all files in a directory recursively using relative paths (Option 1)
     ```
     $ cd $ARCHIVE/data
     $ find . -type f | batch_stage -i
     ```

6. Stage all files in a directory recursively using relative paths (Option 2)
     ```
     $ cd $ARCHIVE
     $ batch_stage -r data
     ```
