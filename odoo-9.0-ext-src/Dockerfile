FROM ubuntu:14.04 
MAINTAINER Filip Nikolov

# Fix for 'debconf: unable to initialize frontend: Dialog' error.
ENV DEBIAN_FRONTEND=noninteractive

# Generate locales
RUN locale-gen en_US.UTF-8 && update-locale
RUN echo 'LANG="en_US.UTF-8"' > /etc/default/locale

# Install some deps, lessc and less-plugin-clean-css, and wkhtmltopdf
RUN apt-get update \
        && apt-get install -y \
            adduser \
            ghostscript \
		    python \
		    python-pip \
		    python-imaging \
		    python-pychart python-libxslt1 xfonts-base xfonts-75dpi \
		    libxrender1 libxext6 fontconfig \
		    python-zsi \
		    python-lasso \
		    libzmq3 \
		    # libpq-dev is needed to install pg_config which is required by psycopg2
		    libpq-dev \
		    # These libraries are needed to install the pip modules
		    python-dev \
		    libffi-dev \
		    libxml2-dev \
		    libxslt1-dev \
		    libldap2-dev \
		    libsasl2-dev \
		    libssl-dev \
		    # Librairies required for LESS
		    node-less \
		    nodejs \
		    npm \
		    # This library is necessary to upgrade PIL/pillow module
		    libjpeg8-dev \ 
		    curl \
		    python-pip 

# Install Python deps.
RUN curl -o /tmp/requirements.txt -SL https://raw.githubusercontent.com/odoo/odoo/9.0/requirements.txt
RUN pip install -r /tmp/requirements.txt

# Install wkhtmltopdf.
RUN curl -o wkhtmltox.deb -SL http://download.gna.org/wkhtmltopdf/0.12/0.12.1/wkhtmltox-0.12.1_linux-trusty-amd64.deb \
        && echo '7dd0e9be7e4fa2a58aa7066460927cdb8ba07492 wkhtmltox.deb' | sha1sum -c - \
        && dpkg --force-depends -i wkhtmltox.deb \
        && apt-get -y install -f \
#The line below was part of the official odoo Dockerfile - needs to be evaluated.
#        && rm -rf /var/lib/apt/lists/* wkhtmltox.deb
        && rm -rf wkhtmltox.deb

#The line below was part of the official odoo Dockerfile - needs to be evaluated.
#apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false -o APT::AutoRemove::SuggestsImportant=false npm \

# Add odoo user
RUN adduser --uid 555 --disabled-password --gecos "odoo" --shell=/bin/false odoo

# Create forlder structure.
RUN /bin/bash -c "mkdir -p /opt/odoo/{odoo-server,enterprise_addons,additional_addons,data}" \
    && mkdir /etc/odoo/ 

# Pull from git
#RUN cd /opt/odoo/odoo-server \
#    && git clone --depth 1 --branch 9.0 https://github.com/odoo/odoo/ /opt/odoo/odoo-server \
#    && chown -R odoo:odoo /opt/odoo

# Copy entrypoint script and Odoo configuration file
COPY ./entrypoint.sh /
COPY ./openerp-server.conf /etc/odoo/
RUN chown -R odoo:odoo /opt/odoo/ /etc/odoo/openerp-server.conf

# Mount /var/lib/odoo to allow restoring filestore and /mnt/extra-addons for users addons
#RUN mkdir -p /mnt/extra-addons \
#        && chown -R odoo /mnt/extra-addons
VOLUME ["/opt/odoo/odoo-server", "/opt/odoo/enterprise_addons", "/opt/odoo/additional_addons", "/opt/odoo/data"]

# Expose Odoo services
EXPOSE 8069 8071

# Set the default config file
ENV OPENERP_SERVER /etc/odoo/openerp-server.conf

# Set default user when running the container
USER odoo

ENTRYPOINT ["/entrypoint.sh"]
CMD ["/opt/odoo/odoo-server/openerp-server"]
