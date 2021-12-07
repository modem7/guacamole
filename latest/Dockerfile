FROM guacamole/guacamole:latest

RUN apt-get update && apt-get install --no-install-recommends -y \ 
                      powerline \
                      fonts-powerline \
                      && apt-get clean \
                      && rm -rf /var/lib/apt/lists/*