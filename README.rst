README for overlaps
===================

Command line utilities used to perform inclusion, exclusion, inflation, and replacement overlaps with input data.

- lookup.py: Perform an inclusion or exclusion join on a stdin data set and a list of join keys
- replace.py: Perform an inclusion join on a data set with a two column list of keys optionally replacing/appending second column of the join file 
- inflation_join.py: Perform an inclusion join on an input data set inflating the data if multiple matching rows are present in the join file

Usage
-----

.. code-block:: bash

    usage: lookup.py [-h] [-f FILE] [-c COLUMN] [-i INCLUDE]

    optional arguments:
      -h, --help            show this help message and exit
      -f FILE, --file FILE  One column file to lookup against
      -c COLUMN, --column COLUMN
                                        Zero based column number to lookup against
      -i INCLUDE, --include INCLUDE
                                        One or zero to include or disallow values from the
                                        lookup file

.. code-block:: bash

    usage: replace.py [-h] [-f FILE] [-c COLUMN] [-r] [-p PRINTALL]

    optional arguments:
      -h, --help            show this help message and exit
      -f FILE, --file FILE  Two column file to lookup against
      -c COLUMN, --column COLUMN
                            Zero based column number to lookup against
      -r, --replace         Option to replace the field you are matching on
      -p PRINTALL, --printall PRINTALL
                            Print non-matching values with input string

.. code-block:: bash

    usage: inflation_join.py [-h] [-1 COLUMN_1] [-2 COLUMN_2] [-j JOINFILE]
                             [-p PRINTALL]

    optional arguments:
      -h, --help            show this help message and exit
      -1 COLUMN_1, --file1 COLUMN_1
                            Stdin column number to join on
      -2 COLUMN_2, --file2 COLUMN_2
                            File two column number to join on
      -j JOINFILE, --joinfile JOINFILE
                            File to join on
      -p PRINTALL, --printall PRINTALL
                            Print non-matching values with input string

Examples
--------

.. code-block:: bash

    $ cat file
    201308	data_type_1	13529
    201309	data_type_1	390
    201310	data_type_1	14145
    201311	data_type_1	23368
    201312	data_type_1	24183
    201401	data_type_1	29616
    201402	data_type_1	23753
    201308	data_type_2	24474
    201309	data_type_2	9601
    201310	data_type_2	28223
    201312	data_type_2	2239
    201401	data_type_2	89
    201402	data_type_2	11123
    $ cat lookupfile
    data_type_1
    $ cat file | lookup.py -f lookupfile -c 1 -i 1
    201308	data_type_1	13529
    201309	data_type_1	390
    201310	data_type_1	14145
    201311	data_type_1	23368
    201312	data_type_1	24183
    201401	data_type_1	29616
    201402	data_type_1	23753
    $ cat replacefile
    data_type_2	foo
    $ cat file | replace.py -f replacefile -c 1 -r
    201308	foo	24474
    201309	foo	9601
    201310	foo	28223
    201312	foo	2239
    201401	foo	89
    201402	foo	11123
    $ cat file | replace.py -f replacefile -c 1 -r -p bar
    201308	data_type_1	13529	bar
    201309	data_type_1	390	bar
    201310	data_type_1	14145	bar
    201311	data_type_1	23368	bar
    201312	data_type_1	24183	bar
    201401	data_type_1	29616	bar
    201402	data_type_1	23753	bar
    201308	foo	24474
    201309	foo	9601
    201310	foo	28223
    201312	foo	2239
    201401	foo	89
    201402	foo	11123
    $ cat inflate_file
    data_type_2	foo	bar
    data_type_2	foo	star
    data_type_2	foo	car
    $ cat file | inflation_join.py -1 1 -2 0 -j inflate_file
    201308	data_type_2	24474	foo	bar
    201308	data_type_2	24474	foo	star
    201308	data_type_2	24474	foo	car
    201309	data_type_2	9601	foo	bar
    201309	data_type_2	9601	foo	star
    201309	data_type_2	9601	foo	car
    201310	data_type_2	28223	foo	bar
    201310	data_type_2	28223	foo	star
    201310	data_type_2	28223	foo	car
    201312	data_type_2	2239	foo	bar
    201312	data_type_2	2239	foo	star
    201312	data_type_2	2239	foo	car
    201401	data_type_2	89	foo	bar
    201401	data_type_2	89	foo	star
    201401	data_type_2	89	foo	car
    201402	data_type_2	11123	foo	bar
    201402	data_type_2	11123	foo	star
    201402	data_type_2	11123	foo	car

