## Post-Install: Create TBS CRs
In order to stage applications, we need to create the following secret and kpack CRs in the `cf` namespace created by the TAS Adapter install package.

1. Create Image Registry Secret - this secret is used to push/pull images from the registry specified earlier in the `tas-adapter-values.yml` file
    ```
    kubectl create secret docker-registry image-registry-credentials \
            -n cf \
            --docker-server=<DOCKER_SERVER> \
            --docker-username=<DOCKER_USERNAME> \
            --docker-password=<DOCKER_PASSWORD>"
    ```
2. Create and kubectl apply a service_account.yaml
    ```
    ---
    apiVersion: v1
    kind: ServiceAccount
    metadata:
    name: kpack-service-account
    namespace: cf
    secrets:
    - name: image-registry-credentials
    imagePullSecrets:
    - name: image-registry-credentials
    ```
3. Create and kubectl apply a cluster_store.yaml
    ```
    apiVersion: kpack.io/v1alpha2
    kind: ClusterStack
    metadata:
    name: cf-default-stack
    spec:
    id: "io.buildpacks.stacks.bionic"
    buildImage:
        image: "paketobuildpacks/build:base-cnb"
    runImage:
        image: "paketobuildpacks/run:base-cnb"

    ```
4. Create and kubectl apply a cluster_stack.yaml
    ```
    apiVersion: kpack.io/v1alpha2
    kind: ClusterStore
    metadata:
    name: cf-default-buildpacks
    spec:
    sources:
    - image: gcr.io/paketo-buildpacks/java:5.21.1
    - image: gcr.io/paketo-buildpacks/nodejs
    - image: gcr.io/paketo-buildpacks/ruby
    - image: gcr.io/paketo-buildpacks/procfile:4.4.1
    - image: gcr.io/paketo-buildpacks/go
    ```
5. Create and kubectl apply a cluster_builder.yaml- **replace the tag to match your container registry! (should match tas-adapter-values.yml registry)**
    ```
    apiVersion: kpack.io/v1alpha2
    kind: ClusterBuilder
    metadata:
    name: cf-kpack-cluster-builder
    spec:
    serviceAccountRef:
        name: kpack-service-account
        namespace: cf
    # Replace with real docker registry
    tag: “<REPLACE-WITH-PACKAGE-REGISTRY-BASE>/builder”
    stack:
        name: cf-default-stack
        kind: ClusterStack
    store:
        name: cf-default-buildpacks
        kind: ClusterStore
    order:
    - group:
        - id: paketo-buildpacks/java
    - group:
        - id: paketo-buildpacks/go
    - group:
        - id: paketo-buildpacks/nodejs
    - group:
        - id: paketo-buildpacks/ruby
    - group:
        - id: paketo-buildpacks/procfile
    ```
