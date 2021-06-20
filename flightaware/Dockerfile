FROM balenalib/amd64-debian:buster

ENTRYPOINT []

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update -qy \
  && apt-get install --no-install-recommends -qy \
  apt-utils \
  wget \
  git \
  devscripts \
  build-essential \
  debhelper \
  tcl8.6-dev \
  autoconf \
  python3-dev \
  python3-venv \
  dh-systemd \
  libz-dev \
  libboost-system-dev \
  libboost-program-options-dev \
  libboost-regex-dev \
  libboost-filesystem-dev \
  net-tools \
  tclx8.4 \
  tcllib \
  libssl-dev \
  chrpath \
  tcl-dev \
  itcl3

RUN git clone https://github.com/flightaware/tcltls-rebuild.git \
  && cd tcltls-rebuild/tcltls-1.7.16 \
  && dpkg-buildpackage -b \
  && sudo dpkg -i ../tcl-tls_1.7.16-1+fa1_amd64.deb

RUN git clone https://github.com/flightaware/piaware_builder.git

RUN piaware_builder/sensible-build.sh buster \
  && cd piaware_builder/package-buster \
  && dpkg-buildpackage -b \
  && sudo dpkg -i ../piaware*.deb \
  && sudo piaware-config allow-auto-updates yes \
  && sudo piaware-config allow-manual-updates yes

COPY ./piaware.conf /etc/piaware.conf
COPY ./start.sh start.sh
RUN chmod +x start.sh

EXPOSE 30105
EXPOSE 30106

CMD ["/start.sh"]
