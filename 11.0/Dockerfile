FROM debian:stretch

# Install some deps, lessc and less-plugin-clean-css, and wkhtmltopdf
RUN set -x; \
        apt-get update \
        && apt-get install -y --no-install-recommends \
            ca-certificates \
            curl \
            adduser \
            lsb-base \
            node-less \
            postgresql-client \
            python3-babel \
            python3-dateutil \
            python3-decorator \
            python3-docutils \
            python3-feedparser \
            python3-html2text \
            python3-pil \
            python3-jinja2 \
            python3-lxml \
            python3-mako \
            python3-mock \
            python3-openid \
            python3-passlib \
            python3-psutil \
            python3-psycopg2 \
            python3-pydot \
            python3-pyparsing \
            python3-pypdf2 \
            python3-reportlab \
            python3-requests \
            python3-suds \
            python3-tz \
            python3-vatnumber \
            python3-vobject \
            python3-werkzeug \
            python3-xlsxwriter \
            python3-yaml \
            python3-pyldap \
            python3-gevent \
            python3-setuptools \
            python3-pip \
            unzip \
        && pip3 install num2words


RUN curl -o wkhtmltox.tar.xz -SL https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.4/wkhtmltox-0.12.4_linux-generic-amd64.tar.xz \
    && apt install xz-utils \
    && tar xf wkhtmltox.tar.xz \
    && cp -R wkhtmltox/* /usr/

# Install Odoo
RUN set -x; \
        curl -o odoo.zip -SL https://github.com/odoo/odoo/archive/11.0.zip \
        && unzip -qq odoo.zip \
        && mkdir -p /usr/lib/python3.5/dist-packages/odoo \
        && mv odoo-11.0/odoo /usr/lib/python3.5/dist-packages/ \
        && mv odoo-11.0/addons/* /usr/lib/python3.5/dist-packages/odoo/addons \
        && mv odoo-11.0/odoo-bin /usr/bin/odoo \
#        && ( cd odoo-11.0 ; python3 setup.py install ) \
        && rm -rf odoo-11.0 odoo.zip

RUN adduser --system --home /var/lib/odoo --quiet --group odoo

RUN chown odoo:odoo -R /usr/lib/python3.5/dist-packages/odoo

# Copy entrypoint script and Odoo configuration file
COPY ./entrypoint.sh /
COPY ./odoo.conf /etc/odoo/
RUN chown odoo /etc/odoo/odoo.conf

# Mount /var/lib/odoo to allow restoring filestore and /mnt/extra-addons for users addons
RUN mkdir -p /mnt/extra-addons \
        && chown -R odoo /mnt/extra-addons
VOLUME ["/var/lib/odoo", "/mnt/extra-addons"]

# Expose Odoo services
EXPOSE 8069 8071

# Set the default config file
ENV ODOO_RC /etc/odoo/odoo.conf

# Set default user when running the container
USER odoo

ENTRYPOINT ["/entrypoint.sh"]
CMD ["odoo"]
