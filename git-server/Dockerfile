FROM alpine:3.4

MAINTAINER Carlos Bernárdez "carlos@z4studios.com"

# "--no-cache" is new in Alpine 3.3 and it avoid using
# "--update + rm -rf /var/cache/apk/*" (to remove cache)
RUN apk add --no-cache \
  openssh \
  git

# Key generation on the server
RUN ssh-keygen -A

# SSH autorun
# RUN rc-update add sshd

# -D flag avoids password generation
# -s flag changes user's shell
RUN mkdir -p /git-server/keys \
  && mkdir -p /git-server/repos \
  && adduser -D -s /usr/bin/git-shell git \
  && echo git:12345 | chpasswd \
  && mkdir -p /home/git/.ssh

RUN mkdir /git-server/repos/granger_repo && \
    git init /git-server/repos/granger_repo --bare && \
    chown git:git -R /git-server/repos/granger_repo

# This is a login shell for SSH accounts to provide restricted Git access.
# It permits execution only of server-side Git commands implementing the
# pull/push functionality, plus custom commands present in a subdirectory
# named git-shell-commands in the user’s home directory.
# More info: https://git-scm.com/docs/git-shell
COPY git-shell-commands /home/git/git-shell-commands

# sshd_config file is edited for enable access key and disable access password
COPY sshd_config /etc/ssh/sshd_config
COPY start.sh start.sh

EXPOSE 22

CMD ["sh", "start.sh"]