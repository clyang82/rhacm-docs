---

copyright:
  years: 2018, 2019
lastupdated: "2019-11-25"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:child: .link .ulchildlink}
{:childlinks: .ullinks}


# Creating your own self-signed and CA Issuers

To create certificates in {{site.data.keyword.product_tm}} that are managed by Certificate manager, you must first create an Issuer.

Create a self-signed Issuer, then use that Issuer to create a CA certificate. Your CA certificate can be managed by Certificate manager.

1. Create a self-signed Issuer. Use the following `.yaml` file to define a self-signed Issuer.

  ```
  apiVersion: certmanager.k8s.io/v1alpha1
  kind: Issuer
  metadata:
    name: hello-myself-tls
    namespace: foobar
  spec:
    selfSigned: {}
  ```
  {: codeblock}

2. After you create the self-signed Issuer, create a CA certificate that references the self-signed Issuer and specifies the `isCA` field.

  ```
  apiVersion: certmanager.k8s.io/v1alpha1
  kind: Certificate
  metadata:
    name: hello-ca-tls
    namespace: foobar
  spec:
    # name of the tls secret to store
    # the generated certificate/key pair
    secretName: hello-deployment-tls-ca-key-pair
    isCA: true
    issuerRef:
      # issuer created in step 1
      name: hello-myself-tls
      kind: Issuer
    commonName: "foo1.bar1"
    dnsNames:
    # one or more fully-qualified domain name
    # can be defined here
    - foo1.bar1
  ```
  {: pre}

3. Edit the following sample of an Issuer that references the previous secret. Edit the `name` and `namespace` from the _metadata_ section of the `.yaml` file. Be sure that `secretName` from the _spec_ section matches the `secretName` from the previous step:

  ```
  apiVersion: certmanager.k8s.io/v1alpha1
  kind: Issuer
  metadata:
    name: hello-deployment-tls
    namespace: foobar
  spec:
    ca:
      secretName: hello-deployment-tls-ca-key-pair
  ```
  {: codeblock}

The `hello-deployment-tls-ca-key-pair` secret was created by Certificate manager and is managed, along with other certificates. This CA certificate can be used by the `hello-deployment-tls` Issuer.

Alternatively, you can also provide the CA certificate and private key instead of using Certificate Manager to create it. Run the following command to create your secret with your CA certificate and key pair files: `ca.crt` and `ca.key`.

```
kubectl create secret tls hello-deployment-tls-ca-key-pair --cert=ca.crt --key=ca.key --namespace=foobar
```
{: codeblock}

Specify this Secret `hello-deployment-tls-ca-key-pair` in the Issuer `.yaml` file that you created earlier in step 3.

You can create a Certificate that is signed by this Issuer by changing the Certificate's `issuerRef.name` to the name of this Issuer `hello-deployment-tls`. For more information, see [Adding a certificate to a Kubernetes workload](create_cert.md#adding_workload).

See [About Red Hat Advanced Cluster Management for Kubernetes Certificate manager (cert-manager)](cert_manager.md) for more Certificate manager topics.