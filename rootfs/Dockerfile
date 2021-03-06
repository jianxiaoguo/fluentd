FROM docker.io/library/fluentd:v1.9-1

USER root

COPY fluentd /fluentd
COPY entrypoint.sh /bin/
RUN chmod +x /bin/entrypoint.sh
ENV FLUENTD_CONF="/fluentd/etc/fluent.conf"

RUN apk add bash findutils \
    && apk add --update --virtual .build-deps sudo build-base ruby-dev \
    && gem install bundler:2.1.4 \
    && bundle config --global silence_root_warning 1 \
    && bundle install --gemfile=/fluentd/drycc-output/Gemfile \
    && rake --rakefile=/fluentd/drycc-output/Rakefile build \
    && fluent-gem install --no-document fluent-plugin-kubernetes_metadata_filter -v 2.5.2 \
    && fluent-gem install --no-document fluent-plugin-elasticsearch -v 4.1.1 \
    && fluent-gem install --no-document fluent-plugin-remote_syslog -v 1.0.0 \
    && fluent-gem install --no-document fluent-plugin-sumologic-mattk42 -v 0.0.4 \
    && fluent-gem install --no-document fluent-plugin-gelf-hs -v 1.0.8 \
    && fluent-gem install --no-document influxdb -v 0.8.0 \
    && fluent-gem install --no-document nsq-ruby -v 2.3.1 \
    && fluent-gem install --local /fluentd/drycc-output/pkg/fluent-plugin-drycc_output-0.1.0.gem \
    && sudo gem sources --clear-all \
    && apk del .build-deps \
    && rm -rf /var/cache/apk/* /root/.gem/ruby/*/cache/*.gem

ENTRYPOINT ["/bin/entrypoint.sh"]

CMD ["/fluentd/sbin/boot"]
