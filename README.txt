
Neo4j Python Bindings
========================

:synopsis: Access Neo4j graph database functionality from python code

Only tested with Neo4j 1.3 on OSX Snow Leopard, Python 2.6

Installation
------------

Two step process

1) Build Neo4j native bindings with JCC (jcc must be installed)

$ cd ${NEO4PY_ROOT}/neo4j
$ ./build-install.sh ${PATH_TO_DOWNLOAD_OF_NEO4J}

(This may require sudo, depending on your configuration)
For example: $ sudo ./build-install.sh ~/src/neo4j-community-1.3

2) 
$ cd ..

Run some tests:
$ python neo4py/testing/graph_core.py

Hopefully no errors!  If there are, send me the output.

and install

$ python setup.py install

(May also need to be sudden)


Getting started
---------------

Simplest way is to use the 'global' graph.
  >>> import neo4py
  >>> gdb = neo4py.init_graph('test-graph.neo4j')
  >>> gdb.shutdown()

This global graph may be accessed from anywhere after being initialized with
>>> gdb = neo4py.get_graph()


Transactions
------------

Transactions are handled differently than in neo4j.py

  >>> tx, created = gdb.get_tx()

If created is True, it is the responsibility of this scope to commit the transaction with any of

  >>> tx.finish(True)	# success
  >>> tx.finish(False)	# failure - rollback changes

  >>> tx.success()		# or .failure()
  >>> tx.finish()		# it doesn't matter if True of False is passed here -- it will be ignored


Nodes, Relationships and Properties
----------------------------------

Much of the syntax is compatible with neo4j.py

Creating a node::			(must be within a transaction!)

  >>> n = gdb.node()

Specify properties for new node::

  >>> n = gdb.node(petals=5, color="Red", height=5.5)			#support for number or string array properties is not yet supported.

Accessing node by id::

  >>> n = gdb.nodes[14]
  

Accessing properties::

  >>> value = n['key'] # Get property value
  
  >>> n['key'] = value # Set property value
  
  >>> del n['key']     # Remove property value
  
  # Or, with a default
  >>> value = n.get('key', 'default')


Besides, a Node object has other attributes::

  >>> for prop, value in n.iteritems()		# loop through node properties
  
  >>>more_props = { "name" : "Jack", "occupation" : "Pilot" }
  >>>n1.update(more_props)
  >>>n2.update(name="Sarah", occupation="Astronaut")
  

  >>> n.id	# Node id
  

Create relationship::

  >>> n1.Knows(n2, since="A long time ago")
  
  # Or
  >>> n1.relationships("Likes")(n2, how_much="A lot") 	# Usefull when the name of
                                          			# relationship is stored in a variable.
					  			# This may change though�obscure syntax


The creation returns a Relationship object, which has properties accessible like nodes.


  >>> rel = n1.Knows(n2, since=123456789)
  
  >>> rel.start		# start node (n1)

  
  >>> rel.end		# end node (n2)
  
  >>> rel.type
  'Knows'
  
  >>> rel['since']
  123456789



Others functions over 'relationships' attribute are possible. Like get all,
incoming or outgoing relationships (typed or not)::

  >>> rels = list(n1.relationships())

  
  >>> rels = list(n1.relationships("Knows", "Likes").incoming)
  
  >>> rels = list(n1.Knows.outgoing.single)


Traversals
----------

In progress
See tests (neo4py/testing/graph_core.py)


Indexes
-------

See tests (neo4py/testing/graph_core.py)

  >>> node_idx = gdb.node_indices.create("My node index", fulltext=True)		# create an new fulltext index
											# Will fail index with name already exists
  >>> node_idx = gdb.node_indices["My node index"]			#retrieve already created index

  >>> "That index" in gdb.node_indices					# test if index exists
False
  >>> "My node index" in gdb.node_indices
True
  
  >>> rel_idx = gdb.rel_indices.create("Relationship index")		# works the same, but for relationships

  >>> node_idx['name', "Jack"] = n1
  >>> node_idx['name', "Jack"] = n500			# two nodes indexed under 'name' => "Jack"

  >>>nodes = list(node_idx['name', 'Jack'])		# Returns iterator over both nodes (exact matching using this syntax)

  >>>nodes = list(node_idx.simple_query('name', 'jack')	# fulltext query by single key/value
  >>>nodes = list(node_idx.query('name:jack'))		# Run lucene query (supports multiple keys)

Relationship indices same, but with a couple extra options (See Neo4j Docs):
  >>> rels = list(rel_idx.simple_query('key', 'value', start_node=n1)		#limit query, for efficiency (can also be end_node)
  >>> rels = list(rel_idx.query('key:value', end_node=some_other_node)


Models, Django support, QuerySets, Aggregates
------

In Progress


Notes
-----

This is a work in progress.  Please let me know if stuff in this document fails� or doesn't seem to be true.

Thanks much.


