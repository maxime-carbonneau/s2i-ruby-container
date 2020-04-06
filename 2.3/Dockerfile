FROM centos/s2i-base-centos7

# This image provides a Ruby 2.3 environment you can use to run your Ruby
# applications.

EXPOSE 8080

ENV RUBY_VERSION 2.3

ENV SUMMARY="Platform for building and running Ruby $RUBY_VERSION applications" \
    DESCRIPTION="Ruby $RUBY_VERSION available as container is a base platform for \
building and running various Ruby $RUBY_VERSION applications and frameworks. \
Ruby is the interpreted scripting language for quick and easy object-oriented programming. \
It has many features to process text files and to do system management tasks (as in Perl). \
It is simple, straight-forward, and extensible."

LABEL summary="$SUMMARY" \
      description="$DESCRIPTION" \
      io.k8s.description="$DESCRIPTION" \
      io.k8s.display-name="Ruby 2.3" \
      io.openshift.expose-services="8080:http" \
      io.openshift.tags="builder,ruby,ruby23,rh-ruby23" \
      com.redhat.component="rh-ruby23-container" \
      name="centos/ruby-23-centos7" \
      version="2.3" \
      usage="s2i build https://github.com/sclorg/s2i-ruby-container.git --context-dir=2.4/test/puma-test-app/ centos/ruby-23-centos7 ruby-sample-app" \
      maintainer="SoftwareCollections.org <sclorg@redhat.com>"

RUN yum install -y centos-release-scl && \
    INSTALL_PKGS="rh-ruby23 rh-ruby23-ruby-devel rh-ruby23-rubygem-rake rh-ruby23-rubygem-bundler" && \
    yum install -y --setopt=tsflags=nodocs $INSTALL_PKGS && rpm -V $INSTALL_PKGS && \
    yum -y clean all --enablerepo='*'

RUN if [[ -n "$IMAGE" ]] ; then yum install -y centos-release-scl && \
    INSTALL_PKGS="ImageMagick" && \
    yum install -y --setopt=tsflags=nodocs $INSTALL_PKGS && rpm -V $INSTALL_PKGS && \
    yum -y clean all --enablerepo='*' \
    ; fi

RUN if [[ -n "$AUDIO" ]] ; then yum localinstall -y --nogpgcheck https://download1.rpmfusion.org/free/el/rpmfusion-free-release-7.noarch.rpm && \
    yum install -y git make cmake gcc-c++ libmad-devel libid3tag-devel libsndfile-devel gd-devel boost-devel && \
    git clone https://github.com/bbc/audiowaveform.git && \
    cd audiowaveform && \
    curl -L -O https://github.com/google/googletest/archive/release-1.10.0.tar.gz && \
    tar xzf release-1.10.0.tar.gz && \
    ln -s googletest-release-1.10.0/googletest googletest && \
    ln -s googletest-release-1.10.0/googlemock googlemock && \
    mkdir build && \
    cd build && \
    cmake .. && \
    make && \
    make install && \
    cd .. && \
    rm -rf audiowaveform \
    ; fi

# Copy the S2I scripts from the specific language image to $STI_SCRIPTS_PATH
COPY ./s2i/bin/ $STI_SCRIPTS_PATH

# Copy extra files to the image.
COPY ./root/ /

# Drop the root user and make the content of /opt/app-root owned by user 1001
RUN chown -R 1001:0 ${APP_ROOT} && chmod -R ug+rwx ${APP_ROOT} && \
    rpm-file-permissions

USER 1001

# Set the default CMD to print the usage of the language image
CMD $STI_SCRIPTS_PATH/usage
