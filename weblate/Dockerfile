FROM debian:jessie-backports
MAINTAINER Michal Čihař <michal@cihar.com>

# Add user early to get a consistent userid
RUN useradd --shell /bin/sh --user-group weblate

RUN env DEBIAN_FRONTEND=noninteractive apt-get update \
  && apt-get install --no-install-recommends -y ssh curl python-virtualenv python-lxml python-pillow python-mysqldb gettext mysql-server \
  && apt-get -y install -t jessie-backports mercurial git python-django-compressor git-svn \
  && apt-get -y install -t python-requests-oauthlib python-six python-openid \
  && apt-get -y install -t python-compressor \
  && apt-get -y upgrade \
  && apt-get clean

WORKDIR /app
RUN python -m virtualenv --system-site-packages .
ADD requirements.txt /tmp/requirements.txt
RUN bin/pip install -r /tmp/requirements.txt
RUN rm /tmp/requirements.txt

ADD settings.py /app/etc/
RUN chmod a+r /app/etc/settings.py
ADD start /app/bin/
RUN chmod a+rx /app/bin/start

WORKDIR /tmp

RUN install -d -o weblate -g weblate -m 755 /app/data
RUN ln -s /app/etc/settings.py /app/local/lib/python2.7/site-packages/weblate/settings.py
ENV DJANGO_SETTINGS_MODULE weblate.settings
ENV START_CMD "/app/bin/django-admin"

VOLUME ["/app/etc", "/app/data"]
USER weblate

EXPOSE 80
ENTRYPOINT ["/app/bin/start"]
CMD ["runserver", "0.0.0.0:80"]
