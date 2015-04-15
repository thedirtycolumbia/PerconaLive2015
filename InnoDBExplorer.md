## EXPLORING YOUR DATA WITH INNODB EXPLORER
_Jeremy Cole (Sr. Systems Engineer, Google)_

https://blog.jcole.us/innodb
https://github.com/jeremycole/innodb_ruby

It is difficult to figure out which command line arguements to pass to the `innodb_space` tool.

The new direction of `innodb_ruby` is a more interactive tool called `innodb_explorer`.

The basic idea is that you run the tool and pass it `-s /path/to/ibdata1` and optionally `-t dbname/tablename`.

This tool includes a web interface for exploring InnoDB data structures interactively.

You can use this tool to visually explore the actual page and record layout of indexes inside of InnoDB.

They've chosen to only pull data from InnoDB and not MySQL (this will be better when the data dict is all in InnoDB).

There are some types, such as DECIMAL, that can't be decoded by InnoDB-only because it requires the data dict.

You don't have to be able to parse data types in InnoDB in order to perform comparison operations.

It is not recommended to create an index on any random string (e.g. a UUID field) because it randomized the index.

A better solution is to use a unique identifier with ordered high bits as per https://github.com/twitter/snowflake

You also run into trouble when you have a secondary index that imposes a different order from the primary key.

See: http://blog.jcole.us/2014/10/02/visualizing-the-impact-of-ordered-vs-random-index-insertion-in-innodb/

Underfilled pages due to random insertion or overfilled pages can cause unexpected table size changes in migration.
