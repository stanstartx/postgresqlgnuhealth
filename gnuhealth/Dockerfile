FROM debian:stretch-slim
MAINTAINER APSARL

ENV SERIES 3.2.9
ENV TYRTON_SERIES 4.2.10
ENV LANG C.UTF-8
ENV HOME /home/gnuhealth


# Create user
RUN groupadd -r gnuhealth \
    && useradd --no-log-init -r -d /home/gnuhealth -m -g gnuhealth -G staff gnuhealth \
    && mkdir /home/gnuhealth/sao \
    && chown -R gnuhealth:gnuhealth /home/gnuhealth/sao

# Install python modules
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        curl \
        wget \
        apt-transport-https \
        dirmngr \
        build-essential \
        libxml2-dev \
        libxslt1-dev \
        libldap2-dev \
        libsasl2-dev \ 
        bsdtar \
        python-imaging \ 
        libjpeg-dev \
        zlib1g-dev \
        python3-cffi \ 
        libffi-dev \
        python3 \
        python3-pip \
        python3-setuptools \
        python3-lxml \
        python3-genshi \
        python3-polib \
        python3-werkzeug \
        python3-wrapt \
        python3-psycopg2 \
        python3-bcrypt \
        python3-dateutil \
        python3-html2text \
        python3-pypdf2 \
        python3-requests \
        python3-simpleeval \
        python3-stdnum \
        python3-tz \
        python3-zeep \
    && rm -rf /var/lib/apt/lists/*

# Setup python3 to pip and python 
RUN rm -R /usr/bin/python \
    && ln -si /usr/bin/python3 /usr/bin/python

# Download source and install (find variable and local path of the files)
RUN su gnuhealth -c "wget https://ftp.gnu.org/gnu/health/gnuhealth-3.2.9.tar.gz -P $HOME \
    && tar xzf $HOME/gnuhealth-3.2.9.tar.gz -C $HOME/ \
    && cd $HOME/gnuhealth-3.2.9/ \
    && bash $HOME/gnuhealth-3.2.9/gnuhealth-setup install"
    
# Make globally executable

COPY trytond /usr/local/bin/trytond
COPY trytond-admin /usr/local/bin/trytond-admin
COPY trytond-cron /usr/local/bin/trytond-cron
RUN chmod +x /usr/local/bin/trytond \
    && chmod +x /usr/local/bin/trytond-admin \
    && chmod +x /usr/local/bin/trytond-cron \
    && chown -R root:staff /usr/local/bin


#SAO Install
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        git \
        gnupg2 \
    && curl -sL https://deb.nodesource.com/setup_8.x | bash - \
    && apt-get install -y --no-install-recommends \
        nodejs \
    && npm install -g bower \
    && curl https://downloads.tryton.org/4.2/tryton-sao-4.2.10.tgz \
        | tar zxf - -C /home/gnuhealth/sao --strip-components=1 \
    && (cd /home/gnuhealth/sao && bower install --allow-root) \
    && rm -rf /root/.cache /root/.npm /root/.config /root/.local \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get purge -y --auto-remove \
        curl \
        git \
        gnupg2 \
        nodejs \
        wget

COPY entrypoint.sh /
RUN chmod +x /entrypoint.sh
COPY trytond.conf /home/gnuhealth/gnuhealth/tryton/server/config/trytond.conf

# Manager-gnuhealth to configure
# COPY manager-gnuhealth.sh /home/gnuhealth/gnuhealth-{$SERIES}/tryton/server/modules
#RUN chmod +x /home/gnuhealth/gnuhealth-{$SERIES}/tryton/server/modules/manager-gnuhealth.sh


EXPOSE 8000

VOLUME ["/home/gnuhealth"]
ENV TRYTOND_CONFIG=/home/gnuhealth/gnuhealth/tryton/server/config/trytond.conf
USER gnuhealth
ENTRYPOINT ["/entrypoint.sh"]
CMD ["trytond"]
