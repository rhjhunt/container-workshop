# The base container
FROM registry.access.redhat.com/ubi8-minimal:latest

LABEL   name="mdl" \
        version="1.0.0" \
        architecture="x86_64" \
        summary="Open source Markdown link tool in Ruby"
    
# RHEL 8 comes with several versions of ruby, this step enables version 2.7
RUN echo -e "[ruby]\nname=ruby\nstream=2.7\nprofiles=\nstate=enabled\n" > /etc/dnf/modules.d/ruby.module && \
    # Update any out of date packages
    microdnf -y --nodocs update && \
    # Install Ruby
    microdnf -y --nodocs install ruby ruby-devel && \
    # Post package install clean up
    microdnf clean all  && \
    # Remove the yum cache
    rm -rf /var/cache/yum && \
    # Use gem to install the markdownlinter
    gem install --no-document mdl

# When the container starts this is the command executed
ENTRYPOINT ["mdl"]
# These are the options passed to the ENTRYPOINT command
CMD ["--help"]
