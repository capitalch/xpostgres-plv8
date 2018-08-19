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

#####Explaination of the Dockerfile
