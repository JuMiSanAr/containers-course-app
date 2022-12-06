# Dockerfile built for example repository here:
# https://github.com/camptocamp/containers-course-app/tree/dev/backend/python


# Base image taken from DockerHub.
# We choose python because we're building a python backend
# and we'll take most of what we need from it.
# 3-buster because this is a python image made for debian.
# This image is not the final one, it's just used to generate python dependencies
# and all the files / data that will be then passed to the final image (ubuntu).
FROM python:3-buster as builder

# Metadata.
LABEL Maintainer="juan.sanchez@camptocmap.com" Vendor="camptocamp"

# Using workdir to create a new path in the image to be more organized
# but not needed.
WORKDIR /usr/src/app/
# We take all dependencies in our requirements.txt file
# and install them in the the WORKDIR specified above (within the container).
ADD requirements.txt ./
RUN pip install -r requirements.txt

# We need to copy data.json and api.py
# to the directories in the container
# that the application expects:
# data.json needs to be in /etc/backend/ as the python backend takes the file from there.
# api.py needs to be un /usr/src/app/ as this is the WORKDIR specified above, and CMD below uses it.
ADD data.json /etc/backend/
ADD api.py /usr/src/app/api.py

# We run and install the api.py file.
# We do it here as we need the python code source
# and we won't have it in the next stage (ubuntu).
RUN pip install pyinstaller && \
    pyinstaller --onefile api.py

# Create a volume for easier handling of this folder.
VOLUME /tmp

# We create a second stage where we take the image created above
# and build the final image on top of it.
FROM ubuntu

RUN apt-get update && \
    apt-get -y install curl && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Move the files generated in the previous image to the new one.
# /usr/src/app/ is given by the workdir we chose before,
# and dist/api is where pyinstaller installed the api.py file
# (we know this last thing by checking the logs when building the image,
# which say "copying bootloader EXE to /usr/src/app/dist/api").
COPY --from=builder /usr/src/app/dist/api /usr/bin/api

# We also need the data.json in the new image.
# We could either use COPY or ADD, it doesn't matter much (ADD might be simpler, but it has no impact in the final result).
# ADD works here as we don't need the python interpreter for json data.
ADD data.json /etc/backend/
# COPY --from=builder /etc/backend/data.json /etc/backend/data.json

# Expose the port that will be used for the application
# (use the one indicated in the documentation of the app, if any).
EXPOSE 8080

# Health check that tests the app is running in the specified port.
# It runs regularly and will log each time it runs,
# and you can also check the status by inspecting the container in the command line.
HEALTHCHECK --interval=1s CMD \
    curl -s -o /dev/null -w "%{http_code}" localhost:8080 | grep 200 || exit 1

# File that will be launched to execute the application.
# Any attributes that should be added to this application,
# we should add them in a CMD line.
# See more:
# https://docs.docker.com/engine/reference/builder/#understand-how-cmd-and-entrypoint-interact.
ENTRYPOINT ["usr/bin/api"]

# To build: docker build -t backend:latest .
# To run: docker run backend:latest
# (backend:latest is just a tag we give it to simplify launching these commands,
# we could use the image id instead).