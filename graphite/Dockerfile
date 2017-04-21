FROM python:2
ENV     DEBIAN_FRONTEND noninteractive
RUN     apt-get update
RUN     apt-get install supervisor gunicorn -y

RUN     mkdir /src
RUN     git clone https://github.com/graphite-project/whisper.git /src/whisper \
            && cd /src/whisper \
            && python setup.py install

RUN     git clone https://github.com/graphite-project/carbon.git /src/carbon \
            && cd /src/carbon \
            && python setup.py install

RUN     git clone https://github.com/graphite-project/graphite-web.git /src/graphite-web \
            && cd /src/graphite-web \
            && python setup.py install \
            && pip install -r requirements.txt \
            && python check-dependencies.py
# Configure Whisper, Carbon and Graphite-Web
ADD     ./initial_data.json /opt/graphite/webapp/graphite/initial_data.json
ADD     ./local_settings.py /opt/graphite/webapp/graphite/local_settings.py
ADD     ./carbon.conf /opt/graphite/conf/carbon.conf
ADD     ./storage-schemas.conf /opt/graphite/conf/storage-schemas.conf
ADD     ./storage-aggregation.conf /opt/graphite/conf/storage-aggregation.conf
RUN     mkdir -p /opt/graphite/storage/whisper
RUN     mkdir -p /opt/graphite/storage/log/webapp/
RUN     touch /opt/graphite/storage/graphite.db /opt/graphite/storage/index
RUN     chown -R www-data /opt/graphite/storage
RUN     chmod 0775 /opt/graphite/storage /opt/graphite/storage/whisper
RUN     chmod 0664 /opt/graphite/storage/graphite.db
RUN     cp /src/graphite-web/webapp/manage.py /opt/graphite/webapp
RUN     cd /opt/graphite/webapp/ && python manage.py migrate --run-syncdb --noinput

# Configure supervisord
ADD     ./supervisord.conf /etc/supervisor/conf.d/supervisord.conf
CMD     ["/usr/bin/supervisord"]