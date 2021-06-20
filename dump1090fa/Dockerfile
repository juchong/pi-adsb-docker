FROM balenalib/amd64-debian:buster
ENTRYPOINT []

WORKDIR /root

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update -qy \
    && apt-get install --no-install-recommends -qy \
    git-core \
    git \
    wget \
    vim \
    cmake \
    libusb-1.0.0-dev \
    build-essential \
    pkg-config \
    libterm-readline-perl-perl \
    doxygen libtecla-dev \
    libtecla-dev \
    libncurses5-dev \
    help2man pandoc \
    debhelper \
    dh-systemd \
    lighttpd


# not needed to run dump1090-fa but could be useful for debugging issues
RUN git clone git://git.osmocom.org/rtl-sdr.git \
    && mkdir -p ./rtl-sdr/build \
    && cd /root/rtl-sdr/build \
    && cmake ../ -DINSTALL_UDEV_RULES=ON \
    && make -j 4 \
    && make install \
    && ldconfig \
    && cp /root/rtl-sdr/rtl-sdr.rules /etc/udev/rules.d/
    
RUN wget https://flightaware.com/adsb/piaware/files/packages/pool/piaware/p/piaware-support/piaware-repository_4.0_all.deb \
    && sudo dpkg -i piaware-repository_4.0_all.deb

#RUN git clone https://github.com/Nuand/bladeRF.git \
#    && cd bladeRF \
#    && dpkg-buildpackage -b \
#    && sudo dpkg -i ../libbladerf2_2021.03_amd64.deb \
#    && sudo dpkg -i ../libbladerf-dev_2021.03_amd64.deb \
#    && sudo dpkg -i ../libbladerf-udev_2021.03_amd64.deb \
#    && sudo dpkg -i ../bladerf_*.deb

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update -qy \
    && apt-get install -qy librtlsdr-dev

RUN git clone https://github.com/adsbxchange/dump1090-fa.git \
    && cd dump1090-fa \
    && sed -i 's/BLADERF=yes/BLADERF=no/g' debian/rules \
    && sed -i 's/, libbladerf-dev//g' debian/control \
    && sed -i 's/, libbladerf1 (>= 0.2016.06),//g' debian/control \
    && sed -i '29i\\n\noverride_dh_shlibdeps:\n\tdh_shlibdeps --dpkg-shlibdeps-params=--ignore-missing-info' debian/rules \
    && dpkg-buildpackage -b \
    && sudo dpkg -i ../dump1090-fa_3.6.3_*.deb

EXPOSE 8080
EXPOSE 30001
EXPOSE 30002
EXPOSE 30003
EXPOSE 30004
EXPOSE 30005
EXPOSE 30104

WORKDIR /root/dump1090

RUN mkdir -p /run/dump1090-fa

COPY ./start.sh start.sh
RUN chmod +x start.sh

ENV DUMP_LAT="" DUMP_LON="" 

CMD ["/root/dump1090/start.sh"]
