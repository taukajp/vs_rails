# [Choice] Ruby version (use -bullseye variants on local arm64/Apple Silicon): 3, 3.2, 3.1, 3.0, 3-bullseye, 3.2-bullseye, 3.1-bullseye, 3.0-bullseye, 3-buster, 3.2-buster, 3.1-buster, 3.0-buster
ARG VARIANT=3-bullseye
FROM mcr.microsoft.com/vscode/devcontainers/ruby:1-${VARIANT}

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

ARG APP_NAME
ENV APP_NAME=${APP_NAME:-myapp}

WORKDIR /${APP_NAME}

# Set environment
ENV LANG=C.UTF-8 \
    TZ=Asia/Tokyo \
    BUNDLE_JOBS=4 \
    BUNDLE_RETRY=3 \
    BUNDLE_APP_CONFIG=.bundle \
    BUNDLE_PATH=vendor/bundle

# [Choice] Node.js version: none, lts/*, 16, 14, 12, 10
ARG NODE_VERSION="none"
RUN if [ "${NODE_VERSION}" != "none" ]; then su vscode -c "umask 0002 && . /usr/local/share/nvm/nvm.sh && nvm install ${NODE_VERSION} 2>&1"; fi

# [Optional] Uncomment this section to install additional OS packages.
RUN apt-get update -qq && export DEBIAN_FRONTEND=noninteractive && \
    apt-get install --no-install-recommends -y \
    # Install packages
    libjemalloc2 libvips \
    sqlite3 libsqlite3-dev && \
    # Clean up
    apt-get autoremove -y && apt-get clean -y && rm -rf /var/lib/apt/lists /var/cache/apt/archives

COPY Gemfile Gemfile.lock ./

# [Optional] Uncomment this line to install additional gems.
# RUN gem update --system && \
#     bundle install && \
#     chmod -R a+w /usr/local/rvm/gems/default
RUN mkdir -p vendor/bundle && \
    chown -R vscode:vscode vendor && \
    gem update --system && \
    bundle install && \
    rm -rf ~/.bundle/ "${BUNDLE_PATH}"/ruby/*/cache "${BUNDLE_PATH}"/ruby/*/bundler/gems/*/.git
RUN su vscode -c "/usr/local/rvm/bin/rvm fix-permissions"

# Copy application code
COPY . .
