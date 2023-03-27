FROM httpd:2.4

RUN apt update -y && apt upgrade -y && apt autoremove -y && apt clean && rm -rf /var/lib/apt/lists*
RUN rm -f /usr/local/apache2/htdocs/index.html
WORKDIR /usr/local/apache2/htdocs

COPY ./web .