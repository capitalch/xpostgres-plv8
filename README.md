# xpostgres-plv8
**Postgresql 10.x Docker image with Plv8 extension 2.3.4**
Following is the Dockerfile I used. This was basically taken from waldo2188 docker image. I did some minor tweaks to get it working with Plv8 version 2.3.4. I tried with clkao which did not work with me.
The Dockerfile I used

```
FROM postgres:10 AS build
ENV PLV8_VERSION=v2.3.5
RUN apt-get update && apt-get upgrade \
    && apt-get install -y git curl glib2.0 libc++-dev python python-pip libv8-dev postgresql-server-dev-$PG_MAJOR
RUN pip install pgxnclient
RUN pgxn install plv8

FROM postgres:10 AS final
RUN apt-get update && apt-get upgrade \
    && apt-get install -y libc++-dev
COPY --from=build /usr/share/postgresql/10/extension/plcoffee* /usr/share/postgresql/10/extension/
COPY --from=build /usr/share/postgresql/10/extension/plls* /usr/share/postgresql/10/extension/
COPY --from=build /usr/share/postgresql/10/extension/plv8* /usr/share/postgresql/10/extension/
COPY --from=build /usr/lib/postgresql/10/lib/plv8*.so /usr/lib/postgresql/10/lib/
COPY --from=build /usr/lib/postgresql/10/lib/pgxs* /usr/lib/postgresql/10/lib/
COPY --from=build /usr/lib/postgresql/10/bin/pg_config /usr/lib/postgresql/10/bin/pg_config
RUN chmod -R 644 /usr/share/postgresql/10/extension/plcoffee* /usr/share/postgresql/10/extension/plls* /usr/share/postgresql/10/extension/plv8* \
    && chmod 755 /usr/lib/postgresql/10/lib/plv8*.so \
    && chmod 755 /usr/lib/postgresql/10/bin/pg_config
```

##### Explaination of the Dockerfile
It's using postgres:10 as base image. I named it as build. The purpose of this image is just to compile the plv8 from source and keep the respective resultant files in respective folders. While compilation several other items are also installed in this image which increases the image size. I don't want the bulky image. I only want the final plv8 binary after the compilation.
`Run pgxn install plv8` compiles and installs the plv8 from source. The final binaries of plv8 are stored in respective folders in the image which I have given the name as *build*
