FROM debian:11

RUN apt-get update && \
    apt-get install -y git gnupg curl && \
    apt-get clean

ENV USER_ID=1001
ENV HOME=/root

WORKDIR ${HOME}

COPY . ${HOME}/

RUN git clone https://github.com/asdf-vm/asdf.git ${HOME}/asdf --depth 1 --branch v0.11.2

ENV BASH_ENV="${HOME}/asdf/asdf.sh"
# Because asdf is loaded via BASH_ENV, all commands using adsf need to be executed using /usr/bin/env bash -c
SHELL ["/usr/bin/env", "bash", "-c"]

COPY .tool-versions ${HOME}/.tool-versions
# The app only needs yarn and node
RUN sed -i -nr '/node|yarn/p' ${HOME}/.tool-versions && \
    cat ${HOME}/.tool-versions | cut -f 1 -d ' ' | xargs -n 1 asdf plugin-add && \
    asdf plugin-update --all && \
    asdf install && \
    asdf reshim

ADD https://github.com/Yelp/dumb-init/releases/download/v1.2.5/dumb-init_1.2.5_x86_64 /usr/local/bin/dumb-init
RUN chmod +x /usr/local/bin/dumb-init
ENTRYPOINT ["dumb-init", "--"]

EXPOSE 3000
USER ${USER_ID}

CMD ["/usr/bin/env", "bash", "-c", "yarn start"]
