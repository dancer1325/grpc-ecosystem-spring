# Kubernetes Setup

* most of kubernetes DNS providers expose enough information / grpc-java -- resolve the -- addresses of services / run | cluster
  * should ALSO work | OKD/OpenShift
* steps to configure
  1. `deployment.spec.containers[*].ports[*].containerPort` == `grpc.server.port` 

    ````yaml
    [...]
        spec:
          containers:
          - name: my-grpc-server-app
            image: ...
            ports:
            - name: grpc # Use whatever name you want
              containerPort: 9090 # Use the same as `grpc.server.port` (prefer 80, 443 or 9090 default one)
              # Container ports can be re-used by OTHER deployments/pods -> NO reason NOT to use a default one
    [...]
    ````

  2. `service.spec.ports[*].port` == your preferred port

    ````yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: my-grpc-server-app # This name is important, NOT use `grpc-server[-*]` or `grpc-client[-*]`
      namespace: example # This name might be important
    spec:
      selector:
        app: my-grpc-server-app
      ports:
        - name: grpclb # The name is important, if you wish to use DNS-SVC-Lookups (must be grpclb)
          port: 1234 # Remember this port number, unless you use DNS-SVC-Lookups (prefer 80, 443 or 9090)
          targetPort: grpc # Use the port name from the deployment (or just the port number)
          # -> NO reason NOT to use a default one 
    ````

    * Kubernetes creates the environment variables
      * `GRPC_SERVER[_*]_PORT`
      * `GRPC_CLIENT[_*]_PORT`
    * if you do NOT use `hostPort`s -> Service ports can be re-used by OTHER services

  3. configure the channel address / -- refer to the -- service name | your client application config

    ````properties
    ## Choose your matching variant
    # Same namespace (target port=80 or derived by DNS-SVC)
    grpc.client.my-grpc-server-app.address=dns:///my-grpc-server-app
    # Same namespace (different port)
    grpc.client.my-grpc-server-app.address=dns:///my-grpc-server-app:1234
    # Different namespace
    grpc.client.my-grpc-server-app.address=dns:///my-grpc-server-app.example:1234
    # Different cluster
    grpc.client.my-grpc-server-app.address=dns:///my-grpc-server-app.example.svc.cluster.local:1234
    # Format
    grpc.client.my-grpc-server-app.address=dns:///<serviceName>[.<namespace>[.<clusterAddress>]][:<service-port>]
    ````

    * DNS-SVC lookups -> require
      * `grpclb` dependency 
      * service's port name = `grpclb`

----------

[<- Back to Index](index.md)
