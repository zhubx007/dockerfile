# Fork from serverbee/redmine-alpine
FROM alpine
MAINTAINER Shengjing Zhu <zsj950618@gmail.com>

ENV RAILS_ENV=production
RUN set -ex \
    && export BUNDLE_SILENCE_ROOT_WARNING=1 \
    && cd / \
    && apk --update add --virtual .redmine-deps \
         ruby ruby-bundler ruby-bigdecimal ruby-json sqlite-libs tzdata git \
    && apk add --virtual .redmine-builddpes \
         curl build-base ruby-dev sqlite-dev zlib-dev \
    && export REDMINE_TAR=https://github.com/redmine/redmine/archive/master.tar.gz \
    && export THEME_ZIP=$(curl --silent https://api.github.com/repos/akabekobeko/redmine-theme-minimalflat2/releases/latest | grep browser_download_url | sed -E 's/.*"([^\"]+)".*/\1/') \
    && export GOSU_BIN=$(curl --silent https://api.github.com/repos/tianon/gosu/releases/latest | grep '/gosu-amd64\"' | sed -E 's/.*"([^\"]+)".*/\1/') \
    && curl -sSL $GOSU_BIN -o /usr/local/bin/gosu \
    && chmod +x /usr/local/bin/gosu \
    && curl -sSL $REDMINE_TAR | tar xz \
    && mv redmine-master redmine \
    && cd /redmine \
    && rm files/delete.me log/delete.me \
    && echo "$RAILS_ENV:" > config/database.yml \
    && echo "  adapter: sqlite3" >> config/database.yml \
    && echo "  database: files/redmine.sqlite3" >> config/database.yml \
    && bundle install --without development test rmagick \
    && cd public/themes/ \
        && curl -sSL $THEME_ZIP -o minimalflat2.zip \
        && unzip minimalflat2.zip \
        && rm minimalflat2.zip \
    && rm -rf ~/.bundle/ \
    && rm -rf /usr/lib/ruby/gems/*/cache/* \
    && apk --purge del .redmine-builddpes \
    && rm -rf /var/cache/apk/* \
    && adduser -h /redmine -D -H -s /sbin/nologin redmine \
    && chown -R redmine:redmine /redmine

WORKDIR /redmine

VOLUME ["/redmine/files", "/gitrepo"]

COPY entrypoint.sh /usr/local/bin/

ENTRYPOINT ["entrypoint.sh"]

EXPOSE 3000

CMD ["rails", "server", "-b", "0.0.0.0"]
