# Use an official Java runtime image as a parent
FROM eclipse-temurin:17-jdk-jammy AS base

# Install essential development tools
RUN apt-get update && apt-get install -y \
    maven \
    git \
    curl \
    wget \
    gnupg \
    && curl -fsSL https://deb.nodesource.com/setup_18.x | bash - \
    && apt-get install -y nodejs \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Keep environment vars in a file that isn’t committed
ARG AEM_ADMIN_USER=admin
ARG AEM_ADMIN_PASS=admin
ARG AEM_PACKAGES=  # space separated list of packages to preinstall
ENV AEM_ADMIN_USER=${AEM_ADMIN_USER}
ENV AEM_ADMIN_PASS=${AEM_ADMIN_PASS}
ENV TZ=UTC

# AEM install directory
WORKDIR /opt/aem

# Copy the AEM quickstart jar
COPY aem-quickstart-copy.jar /opt/aem/aem-quickstart.jar

# The AEM Docker images rely on the license file being present
COPY aem-license-copy.properties /opt/aem/license.properties

# Expose ports (author:4502, publisher:4503, dispatcher:80)
EXPOSE 4502 4503 80

# Helper script to start the container - using a more reliable check for AEM
HEALTHCHECK --interval=60s --timeout=10s --start-period=120s \
  CMD curl -f http://localhost:4502/system/console/healthcheck?tags=systemchecks || exit 1

# Default command – starts the *author* instance
CMD ["java", "-jar", "aem-quickstart.jar", "-p", "4502", "-u", "admin", "-r", "author", "-w", "-c", "$AEM_ADMIN_PASS"]