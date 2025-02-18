FROM debian:10

RUN apt-get update && apt-get install -y libssl-dev tcpd

COPY Unreal3.2 /home/Unreal3.2

RUN chmod -R 0755 /home/Unreal3.2

EXPOSE 6667

CMD ["/bin/bash", "-c", "/home/Unreal3.2/unreal start && tail -f /dev/null"]


