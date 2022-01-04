FROM guacamole/guacamole:1.4.0

RUN apt-get update && apt-get install --no-install-recommends -y \ 
                      powerline \
                      fonts-powerline \
                      && apt-get clean \
                      && rm -rf /var/lib/apt/lists/*