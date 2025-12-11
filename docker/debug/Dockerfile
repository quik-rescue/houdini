FROM ruby:2.3.7-alpine

ARG USER=appuser

# 1. Install System Dependencies
# 'git' is included to fetch gems from git repositories
RUN apk add --no-cache \
    build-base \
    bash \
    git \
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

# 3. Copy Gemfiles
COPY gems /myapp/gems/
COPY Gemfile /myapp/Gemfile
COPY Gemfile.lock /myapp/Gemfile.lock

# Configure bundler to use system libraries for Nokogiri
RUN bundle config build.nokogiri --use-system-libraries

# 4. Install Specific Bundler Version & Gems
# FIX: We specify '-v 1.17.3' to ensure compatibility with Ruby 2.3 and your lockfile
RUN gem install bundler -v 1.17.3 && \
    bundle install --jobs 4 --retry 3

# 5. Create User and Group
RUN addgroup -g 1000 $USER && \
    adduser -u 1000 -G $USER -s /bin/sh -D $USER

# 6. Fix Permissions
RUN chown -R $USER:$USER /myapp && \
    chown -R $USER:$USER /usr/local/bundle

# 7. Final Configuration
USER $USER
EXPOSE 5000

CMD ["foreman", "start"]
