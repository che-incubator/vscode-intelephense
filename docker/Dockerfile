# Copyright (c) 2019 Red Hat, Inc.
# This program and the accompanying materials are made
# available under the terms of the Eclipse Public License 2.0
# which is available at https://www.eclipse.org/legal/epl-2.0/
#
# SPDX-License-Identifier: EPL-2.0
#
# Contributors:
#   Red Hat, Inc. - initial API and implementation

# This is a Dockerfile allowing to build PHP plugin by using a docker container.

FROM node:10.15.3
ARG GITHUB_TOKEN
ENV PHP_INTELEPHENSE_VERSION v1.0.13

# COPY /src/patch.diff /tmp/patch.diff

RUN if [ ! -z "${GITHUB_TOKEN-}" ]; then \
	export GITHUB_TOKEN=$GITHUB_TOKEN; \
	echo "Setting GITHUB_TOKEN value as provided"; \
    fi; \
    apt-get update && apt-get install -y git \
	libsecret-1-dev \
	python \
	python-dev \
	python-pip \
	python-virtualenv \
	pkg-config \
	libxkbfile-dev \
	&& apt-get -y clean && rm -rf /var/lib/apt/lists/* \
	&& export PKG_CONFIG_PATH=/usr/lib/pkgconfig \
	&& git clone --branch ${PHP_INTELEPHENSE_VERSION} --single-branch https://github.com/bmewburn/vscode-intelephense /tmp/php-intelephense \
	&& cd /tmp/php-intelephense \
    && git status \
    && git log -1 \
	&& npm i yarn && yarn && yarn compile

RUN mkdir /tmp/php-intelephense/extensions \
    && mkdir /tmp/php-intelephense/extensions/php-intelephense \
    && mkdir /tmp/php-intelephense/extensions/php-intelephense/extension \
    && cp -r /tmp/php-intelephense/images /tmp/php-intelephense/extensions/php-intelephense/extension \
    && cp -r /tmp/php-intelephense/node_modules /tmp/php-intelephense/extensions/php-intelephense/extension/node_modules \
	&& mkdir /tmp/php-intelephense/extensions/php-intelephense/extension/out \
    && cp -r /tmp/php-intelephense/out/src /tmp/php-intelephense/extensions/php-intelephense/extension/out/src \
	&& cp /tmp/php-intelephense/LICENSE.txt /tmp/php-intelephense/extensions/php-intelephense/extension \
	&& cp /tmp/php-intelephense/README.md /tmp/php-intelephense/extensions/php-intelephense/extension \
	&& cp /tmp/php-intelephense/CHANGELOG.md /tmp/php-intelephense/extensions/php-intelephense/extension \
	&& cp /tmp/php-intelephense/package.json /tmp/php-intelephense/extensions/php-intelephense/extension \
	&& cd /tmp/php-intelephense/extensions/php-intelephense/extension && tar cf /tmp/che-php-intelephense.tar * \
	&& gzip /tmp/che-php-intelephense.tar

CMD zcat /tmp/che-php-intelephense.tar.gz
