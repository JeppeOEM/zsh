# Lecture 11c: Block And Object Storage

This lecture is a short introduction to block and object storage systems.


## Block Storage

Block storage is the kind of storage you are already familar with in, e.g., the local drives attached to your computers.
Both your local computer and your Linode comes with some amount of block storage.

Block storage is characterized by:

- Block storage has random access (block-wise)
- Block storage is attached to no more than one system at any given time
- For other systems to access block storage, they must make network connections to the host that the block storage is connected to
- Block storage is fast
- Support for all kinds of data: databases, files, etc.
- Block storage is expensive

Usually, a cloud provider offers block storage as an option.
This can be very useful, e.g., you could store a database on extra block storage.
This would enable you to dynamically detach the object storage from one server, and attach it to another server with very minimal downtime (no copy needed).


## Object Storage

Object storage is characterized by:

- Object storage does not have random access, and offers only object (file) level access
- Many systems can connect directly to object storage simultaneously
- A computer can connect directly to the objects storage without the need for a server in between; this also means you don't have to pay directly for CPU cycles to serve object data
- Object storage is somewhat slow[^1]
- Only support for file level access, so no databases, etc.
- Object storage is cheap.

Object storage is ideal for a number of use cases:

- Website assets: images, style sheets, JavaScript files
- User files
- HLS video (served directly as small files on HTTP)


## When To Use What Kind Of Storage

Both block and object storage offers flexibility beyond the basic locally attached storage.

In realistic web projects, both these kinds of storage often come into use for different kinds of data.

There is a lot of money to be saved when using object storage rather than block storage, and as a web developer you must design your systems not only to run some code, but also to integrate well with the infrastructure components at hand.


[^1]: When we say object storage is slow, it is because we can only access object storage via network, rather than the very fast internal buses in modern computers, e.g., *PCI-e*. To a web client, it will be just as fast accessing assets from object storage as block storage, because the data still have to be transported over the network.
