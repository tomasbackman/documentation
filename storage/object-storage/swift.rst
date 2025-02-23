====================
Swift object storage
====================
Swift is the OpenStack projects own block storage implementation. Its fully supported within the platform and you are able to run it from any of our :doc:`../regions-and-availability-zones`, either from one availability zone at a time or both using :doc:`replication <replication>`.

.. Tip::
	The Swift implementation does not have as broad support as the :doc:`S3 <s3>` implementation among various libraries and is also much associated with the OpenStack community (so its less portable). If you don't need Swift (for a particular reason) we therefore recommend going with S3 that does (basically) the same thing but has broader support.

Setting up credentials
----------------------
You are able to use Swift using any API-user and setting up application specific credentials is therefore done in the same manner. See our :doc:`/getting-started/users` article for more information.

Swift client
------------
While you can use the :doc:`/getting-started/managing-your-cloud/cloud-management-portal` or :doc:`/getting-started/managing-your-cloud/openstack-horizon` to create and manage buckets (see :doc:`getting-started`), if you want to manage swift storage in another :doc:`availability zone </storage/regions-and-availability-zones>` than *europe-se-1a* (which is the only one these tools can access), you will need the swift client. 

If you read our guide :doc:`/getting-started/managing-your-cloud/openstack-terminal-client`, chances are that you've already installed the client, this can be tested by running ``$ swift --version``. If not, its available via your python package manager (or your OS package manager) and is usually called ``python-swiftclient``, so in Ubuntu you might install it as such ``$ apt install python-swiftclient``.

To use the client, provided you are also using the OpenStack terminal client, you should be able to re-use the same environmental variables as it does. That means if you are able to run for instance ``$ openstack server list`` in a terminal, you should also be able to run ``$ swift list`` (which will show your buckets in availability zone europe-se-1a) in the same terminal. Please see the :doc:`/getting-started/managing-your-cloud/openstack-terminal-client` article for more information.

Client endpoints
^^^^^^^^^^^^^^^^
When you want to use the client with one of the non-default endpoints - that is when you want to use replicated storage or when you want to use another availability zone, you would use the ``--os-storage-url`` flag with the new endpoint: 

- Run this command: ``$ openstack project list``, save the ID of the project. 
- Run this command: ``$ swift --os-storage-url https://object-eu-se-1b.binero.cloud/swift/v1/AUTH_[PROJECT_ID] list``, replacing the [PROJECT_ID] with the ID from previous step and using the URL from the :doc:`endpoints` article to reach the storage you are interested in.

.. Note::
	Without the ``--os-storage-url`` all requests will go to the non-replicated storage in availability zone **europe-se-1a**.

Creating a bucket
-----------------
To create a bucket via Swift, you would either use the API (not covered in this documentation) or the swift client. To use the latter to create a bucket, follow these steps. Add the ``--os-storage-url`` as per above if you need to change API endpoint.

- Select which :doc:`storage policy <flavors>` you want to use. Save the name.
- Select wether or not to :doc:`replicate <replication>`.
- Select in what :doc:`availability zone <../regions-and-availability-zones>` to store your data, save the name.
- Based on replication (or not) as well as availability zone, choose the right :doc:`endpoint <endpoints>`. Save the endpoint-URL.
- Run this command: ``$ swift post -H "X-Storage-Policy:[FLAVOR_NAME]" [BUCKET_NAME]``, replacing the items in angle brackets with the proper data from previous steps.
- To verify, run this command: ``$ swift stat [BUCKET_NAME]``

.. Note::
	Please read the section above about client :doc:`endpoints <endpoints>` and add the ``--os-storage-url`` flag should you need to use something other than non-replicated storage in availability zone europe-se-1a.

Deleting a bucket
-----------------
To delete a bucket using the swift client, follow these steps:

- Run this command: ``$ swift list``, save the name of the bucket you want to delete.
- Run this command: ``$ swift delete [BUCKET_NAME]``, replace [BUCKET_NAME] with the name of the bucket. 

.. Important:: 
	You will not be asked to verify a deletion and everything in the bucket will get deleted.
