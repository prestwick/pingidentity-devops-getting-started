# Deploy Ping DevOps Charts using Helm

Helm is a package deployment tool for Kubernetes. It can be used with PingDevops to deploy all the components of the Solution with a simple command.

To get started, complete the following steps:

1. Inject your Ping DevOps secrets.  There are a couple of options for injecting a license.

   * Eval License - Use your `PING_IDENTITY_DEVOPS_USER/PING_IDENTITY_DEVOPS_KEY` credentials.  For more infomration on using `ping-devops` utility click [here](https://pingidentity-devops.gitbook.io/devops/devopsutils/pingdevopsutil).

        ```shell
        ping-devops generate devops-secret | kubectl -apply -f -
        ```

   * Using license file - In a controlled environment, the helm chart can be run with a valid license file.  Add the license file as a secret as shown (example using pingfederate):

        ```shell

        kubectl create secret generic pingfederate-license --from-file ./pingfederate.lic

        # You will need to include values (see --set below) when you install a chart
        # instructing the install to use the license secret
        helm install \
            pingfederate \
            --set licenseSecretName=pingfederate-license \
            --set pingdevops.pingfederate.license.acceptEULA=YES \
            ping-devops/pingfederate
        ```

2. Install Helm

    ```shell
    brew install helm
    ```

3. Add Helm Ping DevOps Repo

    ```shell
    helm repo add ping-devops https://helm.pingidentity.com/devops/
    ```

4. List Ping DevOps Charts

    ```shell
    helm search repo ping-devops
    ```

5. Update local machine with latest charts

    ```shell
    helm repo update
    ```

6. Create a `devops-values.yaml` file
    * Simple (Vanilla config) - [devops-values.yaml](./devops-values.yaml)

        ```yaml
        # Default values for PingDevops (pingfederate).
        pigndevops:
          pingfederate:
            license:
              acceptEULA: "YES"
        ```

7. Install a Ping DevOps Chart

Install a chart using the `helm install {release} {chart} ...` using the example
below.  In this case, it is installing a `pingfederate` chart with the release name of
`pf`.

```shell
helm install pf ping-devops/pingfederate -f devops-values.yaml
```

## Accessing Deployments

Components of the release will be prefixed with `pf`.  Use `kubectl` to access the workloads:

View kubernetes resources installed

```shell
kubectl get all

# or

kubectl get pods,svc,deploy,rs,sts,job,ing,pvc
```

View Logs:

```shell
# kubectl logs -f service/{release}-{product}

kubectl logs -f service/pf-pingfederate
```

