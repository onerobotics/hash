# hash

A [hash table](https://en.wikipedia.org/wiki/Hash_table) implementation for FANUC KAREL.

This library allows you the flexibility to define how big your hash tables' keys and values will be, and the routines allow you to have multiple hash tables in potentially separate programs.

This implementation uses [open addressing](https://en.wikipedia.org/wiki/Hash_table#Open_addressing) with [linear probing](https://en.wikipedia.org/wiki/Linear_probing). It does not offer dynamic resizing or any performance analysis. When your table gets full, all insertions will return `false`.


## Usage

Define the `HASH_KEYSIZE` and `HASH_VALSIZE` constants for your hash table.

    CONST
    	HASH_KEYSIZE = 16
    	HASH_VALSIZE = 128

Include the `hash.t.kl` file in your `TYPE` section:

    TYPE
    	%INCLUDE includes/hash.t

Define one or more variables of type `ARRAY OF hashNode` in your `VAR` section:

    VAR
    	tbl100 : ARRAY[100] OF hashNode
    	tbl200 : ARRAY[200] OF hashNode

Include the `hash.h.kl` file in your `ROUTINE` section:

    ROUTINE
    	%INCLUDE includes/hash.h

Then use the `hashPut()`, `hashGet()`, and `hashDel()` routines as necessary:

    PROGRAM exampleProg
    CONST
    	HASH_KEYSIZE = 16
    	HASH_VALSIZE = 128
    TYPE
    	%INCLUDE includes/hash.t
    VAR
    	tbl100 : ARRAY[100] of hashNode
    
    %INCLUDE includes/hash.h
    
    BEGIN
    	-- set 'foo' key of [exampleProg]tbl100 to 'bar'
    	IF NOT(hashPut('foo', 'bar', 'exampleProg', 'tbl100')) THEN
    		WRITE('could not insert foo into tbl100', CR)
    		ABORT
    	ENDIF
    
    	-- get value of 'foo' key in [exampleProg]tbl100
    	val = hashGet('foo', 'exampleProg', 'tbl100') -- val is now 'bar'
    
    	-- overwrite 'foo' key in [exampleProg]tbl100
    	IF NOT(hashPut('foo', 'baz', 'exampleProg', 'tbl100')) THEN
    		WRITE('could not overwrite foo in tbl100', CR)
    		ABORT
    	ENDIF
    	val = hashGet('foo', 'exampleProg', 'tbl100') -- val is now 'baz'
    
    	-- delete 'foo' key from [exampleProg]tbl100
    	IF NOT(hashDel('foo', 'exampleProg', 'tbl100')) THEN
    		WRITE('could not delete 'foo' key from 'tbl100', CR)
    		ABORT
    	ENDIF
    
    	-- attempting to get non-existant keys returns ''
    	val = hashGet('foo', 'exampleProg', 'tbl100') -- val is now ''
    END exampleProg

## Development

You must have ROBOGUIDE installed and the the WinOLPC bin directory
needs to be on your system $PATH.

1. Download [GnuWin](http://gnuwin32.sourceforge.net) if you don't
   already have it
2. Clone the repository
3. Spin up a virtual robot with the KAREL option at localhost (or modify Makefile)
4. Run `make` to build the KAREL binary PC files
5. Run `make deploy` to copy files to robot
6. Run `make test` to run tests with [KUnit](https://github.com/onerobotics/kunit)

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
