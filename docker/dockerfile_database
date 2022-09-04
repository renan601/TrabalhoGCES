FROM ubuntu:20.04

# Install dependencies
RUN apt-get update && apt-get install -y gnupg2 && \
    apt-get install -y curl && \
    apt-get install -y software-properties-common

# Add the PostgreSQL PGP key to verify their Debian packages.
RUN curl -sL https://www.postgresql.org/media/keys/ACCC4CF8.asc \
       | apt-key add -

# Add PostgreSQL's repository
RUN echo "deb http://apt.postgresql.org/pub/repos/apt/ focal-pgdg main" > /etc/apt/sources.list.d/pgdg.list

# Configure Timezone
ENV TZ=America/Sao_Paulo
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Install ``python-software-properties``, ``software-properties-common`` and PostgreSQL 12
RUN apt-get update && apt-get install -y python3-software-properties postgresql-12 postgresql-client-12 postgresql-contrib-12

# Run the rest of the commands as the ``postgres`` user created by the ``postgres-12`` package when it was ``apt-get installed``
USER postgres

# Create a PostgreSQL user and database
RUN    /etc/init.d/postgresql start &&\
    psql --command "CREATE USER renan WITH SUPERUSER PASSWORD 'docker';" &&\
    createdb library_db

# Adjust PostgreSQL configuration so that remote connections are possible.
RUN echo "host all  all    0.0.0.0/0  md5" >> /etc/postgresql/12/main/pg_hba.conf

# Add ``listen_addresses`` to ``/etc/postgresql/12/main/postgresql.conf``
RUN echo "listen_addresses='*'" >> /etc/postgresql/12/main/postgresql.conf

# Expose the PostgreSQL port
EXPOSE 5432

# Add VOLUMEs to allow backup of config, logs and databases
VOLUME  ["/etc/postgresql", "/var/log/postgresql", "/var/lib/postgresql"]

# Set the default command to run when starting the container
CMD ["/usr/lib/postgresql/12/bin/postgres", "-D", "/var/lib/postgresql/12/main", "-c", "config_file=/etc/postgresql/12/main/postgresql.conf"]
