# Helm Deployment

> [!Note]
> Helm is a package manager for Kubernetes that simplifies the deployment and management of applications. It uses "charts" (pre-configured Kubernetes resource templates) to define, install, and upgrade complex Kubernetes applications, making it easier to manage multiple Kubernetes manifests as a single unit.

## A. free5GC Helm

Before starting the RAN/UE pods, the free5GC NF pods must be running first.

Please refer to the [free5GC official user guide](https://free5gc.org/guide/7-free5gc-helm/) to install the free5GC microk8s environment.

The guide also covers setting up the Kubernetes environment that will be used in the following steps.

## B. free-ran-ue Helm

- Clone the Helm chart

    ```bash
    git clone https://github.com/free-ran-ue/fru-helm
    ```

- Install the chart in the same namespace as free5GC

    ```bash
    helm install -n free5gc fru ./fru-helm
    ```

    After use, the `uninstall` command can be used to delete the pods:

    ```bash
    helm uninstall -n free5gc fru
    ```

- Start gNB

    The gNB will automatically start when the pods are initialized and connect to the core network.

- Start UE

    Before starting the UE, you need to create a subscriber in the free5GC web console with the default slice selection value of `01-010203`.

    - Get into the pod

        ```bash
        kubectl exec -it -n free5gc fru-freeranue-ue-xxxxxxxxxx-xxxxx -- sh
        ```

        The pod name can be obtained by:

        ```bash
        kubectl get po -n free5gc
        ```

    - Start UE

        ```bash
        ./free-ran-ue ue -c config/ue.yaml
        ```

    - Ping test

        Open another terminal and enter the pod

        ```bash
        ping -I ueTun0 10.100.100.13
        ```

        `10.100.100.13` is the default N6 interface IP at PSA-UPF-1.

        With a successful ping result, the Helm deployment is complete!

## C. Customized gNB and UE profile

Please refer to: [fru-helm/values.yaml](https://github.com/free-ran-ue/fru-helm/blob/main/values.yaml)

> [!Caution]
> If you customize the fields in `values.yaml`, please make sure that it matches the subscriber data in the web console.
