# Apache is like nginx. 
# We take that tag of the php image for that reason.
FROM php:7.2-apache

ADD *.php /var/www/html/

# NOTE: to put together frontend and backend we need to create a network
# (in this case, using a network we will name "lab"):

# 1. Create network (docker network create lab).
# 2. Add the backend with an explicit name (docker run -d --name backend_container --net lab backend:latest).
# 3. Add the frontend (docker run --net lab frontend:latest).
# 4. Launch the network (docker run --net lab -p 8080:80 -e BACKEND_HOST=backend_container frontend).
