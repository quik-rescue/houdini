FROM ruby:2.3.7-alpine

ARG USER=appuser

# 1. Install System Dependencies
# 'build-base' is required to compile native gem extensions (ffi, nokogiri, pg, etc.)
# 'postgresql-dev' is required for the 'pg' gem
# 'openjdk8' replaces your java.sh
# 'nodejs' replaces your node.sh
RUN apk add --no-cache \
    build-base \
    bash \
    tzdata \
    nodejs \
    postgresql-client \
    postgresql-dev \
    openjdk8 \
    libxml2-dev \
    libxslt-dev

# 2. Setup Directories
RUN mkdir -p /myapp
WORKDIR /myapp

# 3. Copy Gemfiles and Install Gems
# We copy these first to leverage Docker layer caching
COPY gems /myapp/gems/
COPY Gemfile /myapp/Gemfile
COPY Gemfile.lock /myapp/Gemfile.lock

# Configure bundler to use system libraries for xml/xslt (helps with Nokogiri on Alpine)
RUN bundle config build.nokogiri --use-system-libraries

RUN bundle install --jobs 4 --retry 3

# 4. Create User and Group (Alpine Syntax)
# Alpine uses 'adduser' and 'addgroup' differently than Debian
RUN addgroup -g 1000 $USER && \
    adduser -u 1000 -G $USER -s /bin/sh -D $USER

# 5. Fix Permissions
# We change ownership of the app and the bundle directory
RUN chown -R $USER:$USER /myapp && \
    chown -R $USER:$USER /usr/local/bundle

# 6. Final Configuration
USER $USER
EXPOSE 5000

# Ensure the correct path for Node if you rely on ICU data, 
# though usually standard Node on Alpine does not require manual ICU pathing.
# If needed, install 'icu-data-full' and uncomment:
# ENV NODE_ICU_DATA=/usr/lib/node_modules/icu4c-data

CMD ["foreman", "start"]
