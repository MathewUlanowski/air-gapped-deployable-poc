# Start from Ubuntu (could also use Debian or Alpine)
FROM ubuntu:latest

# Noninteractive to skip apt prompts
ENV DEBIAN_FRONTEND=noninteractive

# Install Squid
RUN apt-get update && \
    apt-get install -y squid && \
    rm -rf /var/lib/apt/lists/*

# Copy in our custom Squid config
COPY squid.conf /etc/squid/squid.conf
COPY whitelist.txt /etc/squid/whitelist.txt

# Expose whatever port Squid is listening on (e.g., 3128 or 8443)
EXPOSE 3128

# Run Squid in the foreground (-N). You can add debugging level (-d 1) if needed.
CMD ["squid", "-N"]
