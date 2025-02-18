FROM debian:10

RUN dpkg --add-architecture i386 && \
    apt-get update && \
    apt-get install -y \
      build-essential \
      gcc-multilib \
      libssl-dev \
      libpam0g-dev \
      libssl-dev:i386 \
      libpam0g-dev:i386 \
      wget \
      unzip \
      cron \
      apache2-utils

RUN mkdir -p /usr/share/empty /var/ftp /usr/local/man/man5 /usr/local/man/man8 /usr/local/src

COPY files/vsftpd-2.3.4.tar.gz /usr/local/src/vsftpd-2.3.4.tar.gz
COPY files/Makefile /usr/local/src/vsftpd-2.3.4/Makefile
COPY files/vsftpd_init.d /etc/init.d/vsftpd
COPY templates/vsftpd.conf.j2 /etc/vsftpd.conf

RUN cd /usr/local/src && \
    tar -xzvf vsftpd-2.3.4.tar.gz

RUN cd /usr/local/src/vsftpd-2.3.4 && \
    make CFLAGS="-m32" LDFLAGS="-m32 -lcrypt" && \
    make install

RUN chmod 0755 /etc/init.d/vsftpd

RUN useradd -m -d /var/ftp -s /usr/sbin/nologin -u 507 ftp

EXPOSE 21

CMD ["/bin/bash", "-c", "service vsftpd start && tail -f /dev/null"]

