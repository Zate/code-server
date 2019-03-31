FROM node:8.15.0

# Install VS Code's deps. These are the only two it seems we need.
RUN apt-get update && apt-get install -y \
	libxkbfile-dev \
	libsecret-1-dev

# Ensure latest yarn.
RUN npm install -g yarn@1.13

WORKDIR /src
COPY . .
RUN ls -al


# In the future, we can use https://github.com/yarnpkg/rfcs/pull/53 to make yarn use the node_modules
# directly which should be fast as it is slow because it populates its own cache every time.
RUN yarn && yarn task build:server:binary

# We deploy with ubuntu so that devs have a familiar environment.
# Will install addons and extra tools via RUN based on the following ENV Variables
# CODE_GO = true
# CODE_JS = false
FROM ubuntu:18.04
ARG GO_VERSION=1.12
ARG TZ="Etc/UTC"
ENV DEBIAN_FRONTEND=noninteractive
WORKDIR /root/project
COPY --from=0 /src/packages/server/cli-linux-x64 /usr/local/bin/code-server
EXPOSE 8443
RUN apt-get update && apt-get install -y \
	apt-transport-https \
	ca-certificates \
	curl \
	gnupg \
	openssl \
	net-tools \
	git \
	openssh-client \ 
	locales && \
	apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    echo "$TZ" > /etc/timezone && \
    ln -fs /usr/share/zoneinfo/$TZ /etc/localtime && \
	locale-gen en_US.UTF-8
#setup .ssh - either it was mounted, or we need to generate keys

#setup git using env vars to match ssh keys

	
# We unfortunately cannot use update-locale because docker will not use the env variables
# configured in /etc/default/locale so we need to set it manually.
ENV LANG=en_US.UTF-8


ENTRYPOINT ["code-server"]
