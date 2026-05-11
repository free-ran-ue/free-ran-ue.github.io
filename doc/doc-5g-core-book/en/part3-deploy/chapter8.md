# Chapter 8: free5GC Deployment and Configuration

The [official free5GC website](https://free5gc.org) provides a series of deployment guides. In this chapter, we will focus on deployments using bare-metal hosts (including VMs) as well as Docker-based containerized environments. Rather than only showing how to deploy free5GC, this chapter explains how to configure NF configuration files and compile the source code correctly, helping readers understand why these parameters need to be set. This deeper understanding will make it much easier to adjust deployment architectures when using free5GC for future research or experiments.

> [!Note]
> The default environment assumed throughout this book is Ubuntu 24.04.

## 8.1 Installing the GTP5G Kernel Module

### 8.1.1 GTP5G Installation

The gtp5g Linux kernel module is a critical component in any free5GC deployment. Whether you deploy it on a bare-metal host (including VMs), run the UPF using Docker containers, or even adopt more advanced Kubernetes-based architectures in the future, gtp5g must be installed on the host (including the container host running the UPF) that actually **carries UPF user-plane traffic**.

The reason is: gtp5g is a Linux kernel module. The UPF itself does not directly process all GTP-U packets, instead, it relies on this kernel module to perform packet encapsulation, decapsulation, and forwarding.

1. Install required packages

    ```bash
    sudo apt -y update
    sudo apt -y install git gcc g++ cmake autoconf libtool pkg-config libmnl-dev libyaml-dev
    ```

2. Install GTP5G

    ```bash
    git clone -b v0.9.14 https://github.com/free5gc/gtp5g.git
    cd gtp5g
    make
    sudo make install
    ```

### 8.1.2 Packet Forwarding

Since the kernel module is responsible for forwarding user-plane packets, IP Forward function must be enabled on the host running the UPF.

1. Disable the firewall

    ```bash
    sudo systemctl stop ufw
    sudo systemctl disable ufw
    ```

2. Enable forwarding

    Note that the `dn_interface` field in the second command must be replaced with the name of the host’s outbound network interface.

    ```bash
    sudo sysctl -w net.ipv4.ip_forward=1
    sudo iptables -t nat -A POSTROUTING -o <dn_interface> -j MASQUERADE
    sudo iptables -A FORWARD -p tcp -m tcp --tcp-flags SYN,RST SYN -j TCPMSS --set-mss 1400
    ```

## 8.2 Bare-Metal Deployment (Including VM Deployment)

### 8.2.1 Installing Go

> [!Note]
> The Go version currently used by the official free5GC project is Go 1.26.2.

1. Install Go

    ```bash
    wget https://dl.google.com/go/go1.26.2.linux-amd64.tar.gz
    sudo tar -C /usr/local -zxvf go1.26.2.linux-amd64.tar.gz
    mkdir -p ~/go/{bin,pkg,src}
    # The following assume that your shell is bash:
    echo 'export GOPATH=$HOME/go' >> ~/.bashrc
    echo 'export GOROOT=/usr/local/go' >> ~/.bashrc
    echo 'export PATH=$PATH:$GOPATH/bin:$GOROOT/bin' >> ~/.bashrc
    echo 'export GO111MODULE=auto' >> ~/.bashrc
    source ~/.bashrc
    ```

2. Verify installation

    ```bash
    go version
    ```

    If the Go version is displayed, the installation was successful!

### 8.2.2 Fetching and Building free5GC Source Code

1. Clone the official repository from GitHub

    ```bash
    git clone -j `nproc` --recursive https://github.com/free5gc/free5gc
    ```

    > [!Note]
    >
    > -j `nproc` argument enables parallel downloading for faster cloning!
    >
    > The --recursive option ensures that, in addition to the main free5GC repository, all NFs included as Git submodules are also downloaded.

2. Compilation

    ```bash
    cd free5gc
    make
    ```

3. Modify configuration files

    Before starting free5GC, configuration files for three NFs must be modified: AMF, SMF, and UPF. These changes allow the core network to accept connections from an external gNB.

    - AMF (~/free5gc/config/amfcfg.yaml)

        Replace `ngapIpList` with the IP address of the host’s external network interface. This allows the gNB to connect to the AMF using this IP address.

        ```yaml
        ngapIpList:
          - <your export IP>
        ```

    - SMF (~/free5gc/config/smfcfg.yaml)

        Replace the N3 interface endpoint IP in `interfaces` with the UPF’s user-plane-facing IP. This allows the SMF to inform the gNB where to establish the user-plane connection.

        ```yaml
        interfaces:
          - interfaceType: N3
            endpoints:
              - <your export IP>
        ```

    - UPF (~/free5gc/config/upfcfg.yaml)

        Replace the `ifList` address in `gtpu` with the UPF’s external interface IP so the gNB can connect to the UPF using this IP address.

        ```yaml
        gtpu:
          forwarder: gtp5g
          ifList:
            - addr: <your export IP>
        ```

4. Execution

    After completing the configuration file modifications, you can use the execution scripts provided in the free5GC project to start the entire core network with a single command.

    ```bash
    cd free5gc
    ./run.sh
    ```

    Once started, you will see extensive log output. If no ERROR messages appear, the core network has been successfully launched.

## 8.3 Docker Docker-Based Containerized Deployment

In addition to bare-metal execution, free5GC also provides Docker Compose configurations for quickly deploying the core network. Two approaches are supported: using official images or building images locally.

> [!Caution]
> Before proceeding, install Docker Engine by following the instructions on the [official Docker website](https://docs.docker.com/engine/install/).

### 8.3.1 Using Official Images

1. Clone the free5GC Compose repository

    ```bash
    git clone https://github.com/free5gc/free5gc-compose
    ```

2. Start the Compose deployment

    ```bash
    cd free5gc-compose
    docker compose -f docker-compose.yaml up
    ```

    After the images are pulled, the core network will start automatically, with each NF running in its own container.

3. Stop the Compose deployment

    You can press Ctrl+C to stop the containers, which will display the container shutdown logs. However, you still need to use the `down` command to fully shut down the Compose.

    ```bash
    docker compose -f docker-compose.yaml down
    ```

### 8.3.2 Building Images Locally

1. Clone the free5GC Compose repository

    ```bash
    git clone https://github.com/free5gc/free5gc-compose
    ```

2. Enter the `base` directory and fetch the free5GC source code to build the Docker image

    ```bash
    cd free5gc-compose/base
    git clone -j `nproc` --recursice https://github.com/free5gc/free5gc
    ```

3. Build the Docker images with command `make`

    ```bash
    cd free5gc-compose
    make all
    ```

4. Start the Compose deployment using locally built images

    ```bash
    cd free5gc-compose
    docker compose -f docker-compose-build.yaml up
    ```

5. Stop the Compose deployment

    ```bash
    docker compose -f docker-compose-build.yaml down
    ```

## 8.4 Chapter Summary

In this chapter, we walked through a complete free5GC deployment from a hands-on perspective. We first explained why the gtp5g kernel module must be installed on the host that carries UPF traffic, and demonstrated how to enable IP forwarding and basic NAT / MTU settings so that the UPF can correctly forward user-plane packets. We then covered installing Go, fetching and compiling the free5GC source code on a bare-metal (or VM) environment, and modifying key AMF / SMF / UPF configuration fields related to external gNB connectivity, followed by launching the entire core network using `run.sh`.

Building on this foundation, we demonstrated how to deploy free5GC using Docker Compose, either with official images or locally built images, allowing readers to choose between “bare-metal deployment” and “containerized deployment” based on their needs. With these steps and the underlying rationale clearly understood, you should now be able to repeatedly rebuild and adjust free5GC in your own lab environment, laying a solid foundation for further exploration of RAN, UE, and more advanced networking scenarios.

<div class="chapter-nav">
  <a href="../chapter9/" class="nav-btn nav-next" title="Next: Multi-Node / Multi-UPF Deployment">
    <span class="arrow"></span>
  </a>
</div>
