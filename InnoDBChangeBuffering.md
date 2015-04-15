## INNODB CHANGE BUFFERING
_Davi Arnaut (Senior Software Engineer, LinkedIn)_

The change buffer buffers changes to non-unique secondary indexes.

These modifications happen in a random pattern. Caching the modifications is a benefit to performance.

The system variables of note are `innodb_change_buffering` and `innodb_change_buffer_max_size`.

An insert first goes into the primary key or clustering key and then goes to the secondary indexes.

Buffered changes are merged in batches when relevant secondary index reads happen.

The change buffer is a index stored in the system tablespace.

The clustering key is a space ID and page number where the change would have happened.

Change buffering also involves the caching layer if pages are in the buffer pool.

The space file is a bitmap that maps where changes have happened.
