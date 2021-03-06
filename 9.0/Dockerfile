FROM debian:jessie
MAINTAINER Johan Sebastian Cortes Montenegro

# Install some deps, lessc and less-plugin-clean-css, and wkhtmltopdf
RUN set -x; \
        apt-get update \
        && apt-get install -y --no-install-recommends \
            ca-certificates \
            curl \
            node-less \
            python-gevent \
            python-pip \
            python-renderpm \
            python-support \
            python-watchdog \
            python-crypto \
        && curl -O -SL "https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.1/wkhtmltox-0.12.1_linux-wheezy-amd64.deb" \
        && echo '07d397b32f91722de683e8777df2ddd6c49662c2 wkhtmltox-0.12.1_linux-wheezy-amd64.deb' | sha1sum -c - \
        && dpkg --force-depends -i wkhtmltox-0.12.1_linux-wheezy-amd64.deb \
        && apt-get -y install -f --no-install-recommends \
        && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false -o APT::AutoRemove::SuggestsImportant=false npm \
        && rm -rf /var/lib/apt/lists/* wkhtmltox.deb \
        && pip install psycogreen==1.0

# Install Odoo
ENV ODOO_VERSION 9.0
ENV ODOO_RELEASE 20190401
RUN set -x; \
        curl -O -SL http://nightly.odoo.com/${ODOO_VERSION}/nightly/deb/odoo_${ODOO_VERSION}c.${ODOO_RELEASE}_all.deb \
        && echo "fa1187c3887b7b1fbcd8e2e277c5f648f8d84855 odoo_${ODOO_VERSION}c.${ODOO_RELEASE}_all.deb" | sha1sum -c - \
        && dpkg --force-depends -i "odoo_${ODOO_VERSION}c.${ODOO_RELEASE}_all.deb" \
        && apt-get update \
        && apt-get -y install -f --no-install-recommends \
        && rm -rf /var/lib/apt/lists/* "odoo_${ODOO_VERSION}c.${ODOO_RELEASE}_all.deb"

# Copy entrypoint script and Odoo configuration file
COPY ./entrypoint.sh /
COPY ./openerp-server.conf /etc/odoo/
RUN chown odoo /etc/odoo/openerp-server.conf

# Mount /var/lib/odoo to allow restoring filestore and /mnt/extra-addons for users addons
RUN mkdir -p /mnt/extra-addons \
        && chown -R odoo /mnt/extra-addons
VOLUME ["/var/lib/odoo", "/mnt/extra-addons"]

# Expose Odoo services
EXPOSE 8069 8071

# Set the default config file
ENV OPENERP_SERVER /etc/odoo/openerp-server.conf

# Set default user when running the container
USER odoo

ENTRYPOINT ["/entrypoint.sh"]
CMD ["openerp-server"]
