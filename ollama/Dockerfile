FROM ubuntu:22.04

RUN apt-get update && \
    apt-get install -y vim man wget unzip curl gnupg2 ca-certificates lsb-release apache2-utils \
    ethtool wget build-essential zlib1g cmake pkg-config libglvnd-dev libegl1 libopenblas-dev \
    liblapack-dev linux-headers-generic kmod && \
    rm -rf /var/lib/apt/lists/*

COPY nvidia.conf /etc/modules-load.d/
COPY blacklist-nouveau.conf /etc/modprobe.d/

ARG NVIDIA_GRIDD_CONFIG
ARG NVIDIA_TOKEN

RUN mkdir -p /etc/nvidia/ClientConfigToken && \
    echo "${NVIDIA_GRIDD_CONFIG}" | base64 -d | sed 's|{{ '{{' }}MACHERE}}|{{sub.SUBID}}|' > /etc/nvidia/gridd.conf && \
    echo "${NVIDIA_TOKEN}" | base64 -d > /etc/nvidia/ClientConfigToken/nvidialic.vultrlabs.com

COPY ubuntu_repo.key /tmp/ubuntu_repo.key

RUN echo "deb [signed-by=/usr/share/keyrings/vultr-apprepo.gpg] https://apprepo.vultr.com/debian universal main" > /etc/apt/sources.list.d/vultr-apprepo.list && \
    cat /tmp/ubuntu_repo.key | gpg --dearmor > /usr/share/keyrings/vultr-apprepo.gpg && \
    rm /tmp/ubuntu_repo.key && \
    apt update -y && apt install -y vultr-nvidia-client-drivers && \
    rm -rf /var/lib/apt/lists/*

RUN wget --quiet --show-progress --progress=bar:force https://developer.download.nvidia.com/compute/cuda/12.0.0/local_installers/cuda_12.0.0_525.60.13_linux.run && \
    sh cuda_12.0.0_525.60.13_linux.run --toolkitpath=/usr/local/cuda --toolkit --silent && \
    rm -f cuda_12.0.0_525.60.13_linux.run

# set some environment variable for better NVIDIA compatibility
ENV PATH=/usr/local/nvidia/bin:/usr/local/cuda/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
ENV LD_LIBRARY_PATH=/usr/local/nvidia/lib:/usr/local/nvidia/lib64
ENV NVIDIA_DRIVER_CAPABILITIES=compute,utility

RUN curl -L https://ollama.ai/download/ollama-linux-amd64 -o /usr/bin/ollama && \
    chmod +x /usr/bin/ollama

EXPOSE 11434
ENV OLLAMA_HOST 0.0.0.0

#ENTRYPOINT ["/usr/bin/olloma"]
CMD ["/usr/bin/olloma", "serve"]
