# Preparing your Red Hat Advanced Cluster Management for Kubernetes to discover services

You can configure the Red Hat Advanced Cluster Management for Kubernetes service registry to discover Kubernetes services, Kubernetes ingress services, and Istio services that are in different Red Hat Advanced Cluster Management for Kubernetes managed clusters.

When you have multiple instances of a Kubernetes service, a Kubernetes ingress service, or an Istio service that are managed by Red Hat Advanced Cluster Management for Kubernetes, it is challenging to maintain them. The Red Hat Advanced Cluster Management for Kubernetes service discovery function only discovers Kubernetes services, Kubernetes ingress services, and Istio services that are configured to be discovered.

**Required user type or access level:** Cluster administrator.

When you import your target managed cluster (see [Importing a target managed cluster](https://github.com/open-cluster-management/rhacm-docs/blob/doc_stage/manage_cluster/import.md)) from your Red Hat Advanced Cluster Management for Kubernetes, you can configure your service registry component by editing the `serviceRegistry` section in your cluster import YAML file, for example:

   ```
   ...

   # Configure multicluster service registry feature
   serviceRegistry:
     enabled: true
     dnsSuffix: "mcm.svc"
     plugins: "kube-service"
   ...

   ```

Table 1: The following table lists the parameters and descriptions that are available in the YAML file:

| Parameter | Description | Default value|
|---|---|---|
| enabled| Service registry that is used to discover services that are deployed by Application Deployable among managed clusters.| true |
| dnsSuffix| The suffix of the registry DNS name, which is added to the end of the target clusters dns domain name.|mcm.svc|
| plugins| Comma-separated list of enabled plugins. Supported plugins: `kube-service`, `kube-ingress`, and `istio`. |kube-service|

## Configure DNS

Configure the DNS for each target managed cluster by completing these steps:

**Note:** The DNS configuration is dependent on the kind of your managed cluster. You can consult your cluster administrator for more details. The following steps are based on Red Hat OpenShift Container Platform 4.3:

1. Find the `mcm-svc-registry-dns` service cluster IP by entering the following command, where _rcm-klusterlet-namespace_ is the namespace that contains your registry component:

   ```
   kubectl get -n <rcm-klusterlet-namespace> service mcm-svc-registry-dns -o jsonpath='{.spec.clusterIP}'
   ```

2. Configure your cluster DNS configuration by entering the following command:

   ```
   oc edit dns.operator/default
   ```

3. Enable the forwarding plugin in the `default` configuration. Your content should look similar to the following example, where _mcm-svc-registry-dns-service-cluster-ip_ is the IP address that you identified in step 1:

   ```
   spec:
     servers:

     ...

     - name: mcm-server
       zones:
         - mcm.svc
       forwardPlugin:
         upstreams:
           - <mcm-svc-registry-dns-service-cluster-ip>
   ```

   The value `mcm.svc` is from the value of `dnsSuffix` in the `serviceRegistry` previous configuration.
