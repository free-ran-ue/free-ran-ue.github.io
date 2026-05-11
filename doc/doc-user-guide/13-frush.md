# frush

> [!Note]
> frush (free-ran-ue + shell) is a Bash-like interactive shell that provides a convenient interface for operating free-ran-ue and validating 5G core network, free5GC, behavior.

Besides using free-ran-ue to fully launch a gNB/UE simulator, if you don't want to go through the hassle of creating a UE network interface and simply want to quickly verify whether the data plane works after a UE establishes a PDU Session, frush is an excellent choice that allows you to rapidly validate the core network functionality locally.

## A. Prerequisites

- Golang:

    - free-ran-ue is built, tested and run with `go1.26.2 linux/amd64`
    - If Golang is not installed on your system, please execute the following commands:

        - Install Golang:

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

        - Check Installation. You should see the version information:

            ```bash
            go version
            ```

    - If another version of Golang is installed, please execute the following commands to replace it:

        ```bash
        sudo rm -rf /usr/local/go
        wget https://dl.google.com/go/go1.26.2.linux-amd64.tar.gz
        sudo tar -C /usr/local -zxvf go1.26.2.linux-amd64.tar.gz
        source $HOME/.bashrc
        go version
        ```

## B. Configure free5GC

1. Clone free5GC and build

    ```bash
    git clone -j `nproc` --recursive https://github.com/free5gc/free5gc
    cd free5gc
    make all
    ```

2. Modify NF configuration:

    > [!Note]
    > Here we deploy a local mode free5GC. So we modify the N3 interface ip to `127.0.0.1`.

    - ~/free5gc/config/smfcfg.yaml

        Replace N3 interface's endpoints IP from `127.0.0.8` to your `127.0.0.1`:

        ```yaml
        interfaces:
          - interfaceType: N3
            endpoints:
              - 127.0.0.1
        ```

    - ~/free5gc/config/upfcfg.yaml

        Replace N6 interface address IP from `127.0.0.8` to `127.0.0.1`:

        ```yaml
        gtpu:
          forwarder: gtp5g
          iifList:
            - addr: 127.0.0.1
        ```

3. Check IP Forward is enabled

    If you have rebooted your machine, remember to run these command with setting your export network interface:

    ```bash
    sudo sysctl -w net.ipv4.ip_forward=1
    sudo iptables -t nat -A POSTROUTING -o <export network interface> -j MASQUERADE
    sudo systemctl stop ufw
    sudo iptables -I FORWARD 1 -j ACCEPT
    ```

4. Run free5GC (under `free5gc`)

    ```bash
    ./run.sh
    ```

5. Run webconsole and create a subscriber by default (under `free5gc`)

    ```bash
    cd webconsole
    ./bin/webconsole
    ```

    > [!Important]
    > You don't need to add subscriber in webconsole, frush can do this.

## C. frush

1. Clone frush and build

    ```bash
    git clone https://github.com/free-ran-ue/frush
    cd frush
    make
    ```

2. Run (under `frush`)

    ```bash
    ./frush
    ```

3. Support commands

    | frush CMD | Args | Description |
    | - | - | - |
    | help | - | Show help |
    | exit | - | Exit |
    | add | - | Add a subscriber to free5GC's webconsole |
    | delete | - | Delete a subscriber from free5GC's webconsole |
    | status | - | Show the status of gNB and UE |
    | gnb | - | Start gNB |
    | reg | - | Register UE to free5GC |
    | dereg | - | De-register UE |
    | ping | {IP} | Ping the DN, if dn is not provided, ping 1.1.1.1 and 8.8.8.8 |

## D. Example

For general usage, frush can be run as follows:

1. `add`: add a subscriber
2. `gnb`: start gNB
3. `reg`: register UE
4. `ping`: check data plane
5. `dereg`: de-register UE
6. `delete`: delete subscriber
7. `exit`: leave frush

At any time, the `status` command can be used to check gNB and UE status.
