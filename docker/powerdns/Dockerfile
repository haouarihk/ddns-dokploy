FROM buildpack-deps:bookworm-scm

# 1. Add the PowerDNS repository for Debian 12 (Bookworm) and PowerDNS Auth 4.9
RUN install -d /etc/apt/keyrings \
    && curl https://repo.powerdns.com/FD380FBB-pub.asc | gpg --dearmor > /etc/apt/keyrings/powerdns-keyring.gpg \
    && echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/powerdns-keyring.gpg] http://repo.powerdns.com/debian bookworm-auth-49 main" > /etc/apt/sources.list.d/pdns.list

# 2. Set Pin-Priority to prefer PowerDNS repo over default Debian repos
RUN echo "Package: pdns-*\nPin: origin repo.powerdns.com\nPin-Priority: 600\n" > /etc/apt/preferences.d/pdns

# 3. Update and install
RUN apt-get -y update \
    && apt-get install -y pdns-server pdns-backend-remote \
    && rm -rf /var/lib/apt/lists/*

COPY pdns.conf /etc/powerdns/pdns.conf

COPY entrypoint.sh /
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]

EXPOSE 53/tcp 53/udp

CMD ["pdns_server", "--daemon=no"]