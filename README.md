# docker 

This is odoo 9 docker built with the idea to map odoo-server and addons as volumes to host for ease of management. 
The Dockerfile works, but I will not call it production ready. 

Odoo is using Postgres as db, to start Postgres container run: 
docker run -d -e POSTGRES_USER=odoo -e POSTGRES_PASSWORD=odoo --name db postgres 

Before running container from this image make sure you have odoo source downloaded to folder on the host. Also change ownership to 555:555 (this is the ID of odoo inside the container).

An example of how to run it, (obviously the paths need to change). 
docker run -v /root/docker/source/odoo-server:/opt/odoo/odoo-server -v /root/docker/source/enterprise_addons:/opt/odoo/enterprise_addons -v /root/docker/source/data:/opt/odoo/data -v /root/docker/source/additional_addons:/opt/odoo/additional_addons -p 127.0.0.1:8080:8069 --name odoo --link db:db -t lumnus/odooee:v6
