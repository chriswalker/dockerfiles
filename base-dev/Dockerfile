# FROM alpine:3.10.2
FROM golang:1.12.9-alpine3.10

LABEL maintainer="thatamazingweb@gmail.com"

#
# Install basic tooling
#

# Add edge/testing repo in, required for ripgrep & bat at the moment
RUN echo "http://dl-cdn.alpinelinux.org/alpine/edge/testing" >> /etc/apk/repositories

# Install required tools
RUN apk update && \
    apk add --no-cache \
        bat=0.12.1-r1 \
        curl=7.66.0-r0 \
        fish=3.0.2-r3 \
        git=2.22.0-r0 \
        jq=1.6-r0 \
        man-pages=5.01-r0 \
        man=1.14.3-r3 \
        # npm=10.16.3-r0 \                  # going to refactor vim plugins to remove dep
        # nodejs=10.16.3-r0 \               # going to refactor vim plugins to remove dep
        neovim=0.3.7-r0 \
        # python3=3.7.3-r0 \                # going to refactor vim plugins to remove dep
        ripgrep=11.0.2-r0 \
        tmux=2.9a-r \
        tree=1.8.0-r0 && \
    rm -rf /var/cache/apk/*

#
# Create required user for container
#

# Set these with --build-args
ARG GROUP
ARG USER
ARG UID

# Create staff group & user (user matches user in host)
RUN addgroup staff
RUN adduser $USER --uid $UID --ingroup $GROUP --disabled-password

# Ensure following commands are run as $USER
USER $USER
WORKDIR /home/$USER

#
# Set up $USER's $HOME:
# * Override base image's $GOPATH
# * Install dotfiles repo
# * Link various dotfiles into correct locations
# * Install vim-plug and vim plugins
#

# GOPATH in the base image defaults to /Go; override & set up workspace
ENV GOPATH Dev/Go

# Create directories needed for dotfiles symlinks and dev environment
RUN mkdir -p \
    ".config" \
    ".local/share/fish" \
    "Dev" \
    # Override base image's Go workspace
    "$GOPATH/src" \
    "$GOPATH/pkg" \
    "$GOPATH/bin" && \
    chmod -R 777 "$GOPATH"

# Install required Go tooling
# TODO

# Install dotfiles
RUN git clone https://github.com/chriswalker/dotfiles.git ./dotfiles && \
    ln -fs ~/dotfiles/misc/.ripgreprc ~/.config/ripgrep && \
    ln -fs $HOME/dotfiles/fish $HOME/.config/fish && \
    ln -fs $HOME/dotfiles/nvim $HOME/.config/nvim && \
    ln -fs $HOME/dotfiles/tmux/.tmux.conf .

# Configure neovim (Install vim-plug, then install plugins specified in nvim config)
RUN curl -fLo $HOME/dotfiles/nvim/autoload/plug.vim \
    --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim && \
    nvim +PlugInstall +UpdateRemotePlugins
    # TODO Fix Py3 issue, run GoInstallBinaries for vim-go
    # nvim +GoInstallBinaries +qa!