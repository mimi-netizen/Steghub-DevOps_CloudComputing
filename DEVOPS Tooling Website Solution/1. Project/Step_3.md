# Step 3 — Prepare the Web Servers

We need to make sure that our Web Servers can serve the same content from shared storage solutions, in our case – NFS Server and MySQL
database.

You already know that one DB can be accessed for reads and writes by multiple clients. For storing shared files that our Web Servers
will use – we will utilize NFS and mount previously created Logical Volume lv-apps to the folder where Apache stores files to be served
to the users (/var/www).

This approach will make our Web Servers stateless, which means we will be able to add new ones or remove them whenever we need, and
the integrity of the data (in the database and on NFS) will be preserved.
