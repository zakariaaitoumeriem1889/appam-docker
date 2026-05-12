FROM debian:bookworm-slim

ENV HTTPD_PREFIX=/usr/local/apache2
ENV PATH="$HTTPD_PREFIX/bin:$PATH"

RUN mkdir -p "$HTTPD_PREFIX" \
    && chown www-data:www-data "$HTTPD_PREFIX"

WORKDIR $HTTPD_PREFIX

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    apache2 \
    curl \
    ca-certificates \
    && rm -rf /var/lib/apt/lists/*

RUN a2enmod proxy_fcgi ssl rewrite proxy proxy_balancer proxy_http proxy_ajp headers

RUN sed -i '/Global configuration/a ServerName localhost' \
    /etc/apache2/apache2.conf

# Copy default vhost configs into the prefix location (optional, adjust as needed)
RUN ln -sfn /etc/apache2 "$HTTPD_PREFIX/conf" \
    && ln -sfn /var/log/apache2 "$HTTPD_PREFIX/logs" \
    && ln -sfn /var/run/apache2 "$HTTPD_PREFIX/run"

EXPOSE 80 443

# Remove stale PID at runtime, not build time
CMD ["sh", "-c", "rm -f /run/apache2/apache2.pid && exec apachectl -DFOREGROUND -e info"]