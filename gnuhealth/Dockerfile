FROM debian:stretch-slim
MAINTAINER APSARL

ENV SERIES 4.2
ENV LANG C.UTF-8

RUN groupadd -r trytond \
    && useradd --no-log-init -r -d /var/lib/trytond -m -g trytond trytond \
    && mkdir /var/lib/trytond/db && chown trytond:trytond /var/lib/trytond/db \
    && mkdir /var/lib/trytond/www

COPY entrypoint.sh /
COPY trytond.conf /etc/trytond.conf

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        curl \
        python3 \
        python3-pip \
        python3-setuptools \
        # trytond
        python3-lxml \
        python3-genshi \
        python3-polib \
        python3-werkzeug \
        python3-wrapt \
        python3-psycopg2 \
        python3-bcrypt \
        # modules
        python3-dateutil \
        python3-html2text \
        python3-pypdf2 \
        python3-requests \
        python3-simpleeval \
        python3-stdnum \
        python3-tz \
        python3-zeep \
    && rm -rf /var/lib/apt/lists/*

RUN pip3 install \
    "trytond == ${SERIES}.*" \
    && for module in `curl https://downloads.tryton.org/${SERIES}/modules.txt`; do \
        pip3 install "trytond_${module} == ${SERIES}.*"; \
        done \
    && pip3 install phonenumbers \
    && rm -rf /root/.cache

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        git \
        gnupg2 \
        curl \
    && curl -sL https://deb.nodesource.com/setup_8.x | bash - \
    && apt-get install -y --no-install-recommends \
        nodejs \
    && npm install -g bower \
    && curl https://downloads.tryton.org/${SERIES}/tryton-sao-last.tgz \
        | tar zxf - -C /var/lib/trytond/www --strip-components=1 \
    && (cd /var/lib/trytond/www && bower install --allow-root) \
    && rm -rf /root/.cache /root/.npm /root/.config /root/.local \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get purge -y --auto-remove \
        curl \
        git \
        gnupg2 \
        nodejs

EXPOSE 8000

VOLUME ["/var/lib/trytond/db"]
ENV TRYTOND_CONFIG=/etc/trytond.conf
USER trytond
ENTRYPOINT ["/entrypoint.sh"]
CMD ["trytond"]
