# RPI-Nginx-Reverse-Proxy
Ngninx Reverse Proxy for Raspberry Pi (commands for docker)


#pull image
$ docker image build -t rpi-nginx-reverse-proxy
#run using 80 port
$ docker run -d -p 80:80 --name reverse-proxy rpi-nginx-reverse-proxyecho
