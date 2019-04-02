FROM ubuntu:16.04

# To make it easier for build and release pipelines to run apt-get,
# configure apt to not require confirmation (assume the -y argument by default)
ENV DEBIAN_FRONTEND=noninteractive
RUN echo "APT::Get::Assume-Yes \"true\";" > /etc/apt/apt.conf.d/90assumeyes

# Trusty needs an updated backport of apt to avoid hash sum mismatch errors
RUN [ "xenial" = "trusty" ] \
 && curl -s https://packagecloud.io/install/repositories/computology/apt-backport/script.deb.sh |  bash \
 && apt-get update \
 && apt-get install apt=1.6.9 \
 && rm -rf /var/lib/apt/lists/* \
 && rm -rf /etc/apt/sources.list.d/* \
 || echo -n

RUN apt-get update \
 && apt-get install -y --no-install-recommends \
        software-properties-common \
 && apt-add-repository ppa:git-core/ppa \
 && apt-get update \
 && apt-get install -y --no-install-recommends \
        apt-utils \
        curl \
        git \
        jq=1.5+dfsg-1ubuntu0.1 \
        libcurl3 \
        libicu55 \
        libunwind8 \
        netcat \
 && curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.deb.sh | bash \
 && apt-get install -y --no-install-recommends git-lfs \
 && rm -rf /var/lib/apt/lists/* \
 && rm -rf /etc/apt/sources.list.d/*

# Install basic command-line utilities
RUN apt-get update \
 && apt-get install -y --no-install-recommends \
    dnsutils \
    file \
    ftp \
    iproute2 \
    iputils-ping \
    locales \
    openssh-client \
    rsync\
    shellcheck \
    sudo \
    telnet \
    time \
    unzip \
    wget \
    zip \
    tzdata \
    ansible \
    libxtables11 \
    libnfnetlink0 \
 && rm -rf /var/lib/apt/lists/* 
 
 
 # Install Go
RUN curl -sL https://dl.google.com/go/go1.9.4.linux-amd64.tar.gz -o go1.9.4.linux-amd64.tar.gz \
 && mkdir -p /usr/local/go1.9.4 \
 && tar -C /usr/local/go1.9.4 -xzf go1.9.4.linux-amd64.tar.gz --strip-components=1 go \
 && rm go1.9.4.linux-amd64.tar.gz
RUN curl -sL https://dl.google.com/go/go1.10.linux-amd64.tar.gz -o go1.10.linux-amd64.tar.gz \
 && mkdir -p /usr/local/go1.10 \
 && tar -C /usr/local/go1.10 -xzf go1.10.linux-amd64.tar.gz --strip-components=1 go \
 && rm go1.10.linux-amd64.tar.gz
ENV GOROOT_1_9_X64=/usr/local/go1.9.4 \
    GOROOT_1_10_X64=/usr/local/go1.10 \
    GOROOT=/usr/local/go1.10
ENV PATH $PATH:$GOROOT/bin

# Install Helm
RUN curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get | bash

# Install Powershell Core
RUN curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add - \
 && curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list | tee /etc/apt/sources.list.d/microsoft.list \
 && apt-get update \
 && apt-get install -y --no-install-recommends \
    powershell \
 && rm -rf /var/lib/apt/lists/* \
 && rm -rf /etc/apt/sources.list.d/*

 
 # Install Terraform
RUN TERRAFORM_VERSION=$(curl -s https://checkpoint-api.hashicorp.com/v1/check/terraform | jq -r .current_version) \
 && curl -LO https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_amd64.zip \
 && unzip terraform_${TERRAFORM_VERSION}_linux_amd64.zip -d /usr/local/bin \
 && rm -f terraform_${TERRAFORM_VERSION}_linux_amd64.zip
 
# Install Gcloud CLI
RUN export CLOUD_SDK_REPO="cloud-sdk-$(lsb_release -c -s)" \
 && echo "deb http://packages.cloud.google.com/apt $CLOUD_SDK_REPO main" | sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list \
 && curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add - \
 && sudo apt-get update && sudo apt-get install -y --no-install-recommends google-cloud-sdk

# Install AWS CLI
RUN curl "https://bootstrap.pypa.io/get-pip.py" -o "get-pip.py" \
 && python get-pip.py \
 && pip install awscli
 
# Install kubectl
RUN curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl \
 && chmod +x ./kubectl \
 && mv ./kubectl /usr/local/bin/kubectl
 
 # Install docker-ce
RUN curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - \
 && sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" \
 && apt-get update \
 && apt-cache policy docker-ce \
 && apt-get install -y -f --no-install-recommends \
    docker-ce-cli \
    containerd.io \
    iptables \
    docker-ce
    
# Install Node.js


# Install Yarn
    
 
 
 # Clean system
RUN apt-get clean \
 && rm -rf /var/lib/apt/lists/* \
 && rm -rf /etc/apt/sources.list.d/*

# Accept the TEE EULA
RUN mkdir -p "/root/.microsoft/Team Foundation/4.0/Configuration/TEE-Mementos" \
 && cd "/root/.microsoft/Team Foundation/4.0/Configuration/TEE-Mementos" \
 && echo '<ProductIdData><eula-14.0 value="true"/></ProductIdData>' > "com.microsoft.tfs.client.productid.xml"

WORKDIR /vsts

COPY ./start.sh .
RUN chmod +x start.sh

CMD ["./start.sh"]