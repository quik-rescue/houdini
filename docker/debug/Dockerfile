FROM ruby:2.3.7-alpine

# FIX: Rename ARG to avoid conflict with system 'USER' variable (which is often 'root')
ARG APP_USER=appuser

# 1. Install System Dependencies
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

# Configure bundler
RUN bundle config build.nokogiri --use-system-libraries

# 4. Install Gems
# Using specific bundler version to match your lockfile
RUN gem install bundler -v 1.17.3 && \
    bundle install --jobs 4 --retry 3

# 5. Create User and Group
# We use the internal 'APP_USER' variable here to safely create the user
RUN addgroup -g 1000 $APP_USER && \
    adduser -u 1000 -G $APP_USER -s /bin/sh -D $APP_USER

# 6. Fix Permissions
RUN chown -R $APP_USER:$APP_USER /myapp && \
    chown -R $APP_USER:$APP_USER /usr/local/bundle

# 7. Final Configuration
# Switch to the new user
USER $APP_USER
EXPOSE 5000

CMD ["foreman", "start"]
