# Managing secrets

You can create Kubernetes secret resources to store authorization credentials and other sensitive information for your subscriptions and application components. By storing this information as secrets, you can separate the information from the application components that require the information to improve your data security. Samples for all resources, including secrets, are located in the [Application resource samples](app_resource_samples.md) documentation.

Secrets (`Secret`) are Kubernetes resources that you can use to store authorization and other sensitive information, such as passwords, OAuth tokens, and SSH keys. By storing this information as secrets, you can separate the information from the subscriptions and other application components that require the information to avoid exposing sensitive information to help encrypt your data and better control data security within your application lifecycle.

You can deploy and use secrets on target managed clusters or reference secrets from subscriptions to access channels that require authorization.

If you need to deploy Kubernetes resources or Helm charts to your managed clusters from channels that require authorization, such as entitled GitHub repositories, your subscriptions can reference secrets to provide access to these channels.

For instance, if you need to deploy a Helm chart from an entitled GitHub repository, you can create a secret that includes the credentials for accessing the repository. This secret must be created within the namespace of the channel for the entitled repository. You can then create a subscription to the channel and include a reference to the secret that is within the channel namespace. By including the credentials in the secret and referencing the secret through the subscription, you do not need to include the credentials directly within the subscription definition. The subscription can then pull the secret and use the encrypted credentials within the secret to access the subscribed Helm chart for deployment.

You can also deploy Kubernetes secrets from channels to target managed clusters through subscriptions when you have applications on your clusters that require access to services or data sources that require authorization or other sensitive information. For instance, you might want to include image pull secrets, which include credentials for a Docker image registry, on your clusters. By including image pull secrets in a managed cluster namespace where a subscription to a Helm chart or deployed Pod is included, the Helm chart or pod can then use the pull secret automatically.

When you create and include a secret within a channel for deployment to managed clusters, that secret can be deployed to only the same namespace on the target managed clusters as the channel namespace.

When you use Kubernetes secrets with your subscriptions to access channel sources or deploy secrets through subscriptions, the secret and all related data in rest and data in transit remain encrypted. The Kubernetes secrets are created with the specified data values encrypted. Transport Layer Security (TLS) is used to encrypt the data in transit.

 - [Create a Secret](#create-a-secret)
 - [Update a Secret](#update-a-secret)
 - [Delete a Secret](#delete-a-secret)

## Create a secret

1. Compose the definition YAML content for your secret resource. Samples for all resources, including secrets are located in the [Application resource samples](app_resource_samples.md) documentation.

2. Create the secret within Red Hat Advanced Cluster Management for Kubernetes. You can use Kubernetes command line interface (`kubectl`) tool or REST API:

   - To use the Kubernetes CLI tool, complete the following steps:
     1. Compose and save your secret YAML file with your preferred editing tool.
     2. Run the following command to apply your file to an apiserver. Replace `filename` with the name of your file:
        ```
        kubectl apply -f filename.yaml
        ```

        Alternatively, you can use a `create secret` command to create a secret. For instance, you can use the command when you want a name generated and do not want to specifically name the secret.

     3. Verify that your secret is created, by running the following command:
        ```
        kubectl get secrets
        ```

        Ensure that your new secret is listed in the resulting output.

   - To use REST API, you need to use the Kubernetes API. For more information, see [Secrets](https://kubernetes.io/docs/concepts/configuration/secret/).

### Limitations

- A secret resource must be included within the same namespace as the channel and subscription that are used to store and deploy the secret.
- If you are using the secret as an image pull secret, the secret must be in the same namespace as the pod on the managed cluster where the secret is used. A pod can reference only image pull secrets that are in the same namespace.
- A secret resource is restricted to 1 Mib based on Kubernetes standards.
- A subscription cannot detect changes to a secret and pull the updated secret from a channel and deploy the updated secret to managed clusters. Updates to secrets are only retrieved by a subscription when the subscription needs to directly reference the secret, such as when the subscription detects a new or changed version of the subscribed Kubernetes resource or Helm chart.

## Update a secret

1. Compose the definition updates for your secret. 

2. Update the definition. You can use the console, the Kubernetes command line interface (`kubectl`) tool, or REST API:

   - To use the console search to find and edit a secret,
     1. Open the console.
     2. From the main menu, click **Search**.  
     3. Within the search box, filter by `kind:secret` to view all secrets.
     4. Within the list of all secrets, click the secret that you want to update. The YAML for that secret is displayed.
     5. Click **Edit** to enable editing the YAML content.
     6. When you are finished your edits, click **Save**. Your changes are saved and applied automatically.

   - To use the Kubernetes CLI tool, complete the following steps:
       1. Run the following command against the source secret:
         ```
         kubectl edit secrets <name>
         ```
         
       2. Update any fields or annotations that you need to change.

   - To use REST API, you need to use the Kubernetes API. For more information, see [Secrets](https://kubernetes.io/docs/concepts/configuration/secret/).

When your changes are saved, the changes are not automatically detected. The updated version is not deployed to any destination clusters where the version was previously deployed until the secret is required.

## Delete a secret

You can use the console, the Kubernetes command line interface (`kubectl`) tool, or REST API to delete a secret:

   - To use the console search to find and delete a secret,
     1. Open the console.
     2. From the main menu, click **Search**.  
     3. Within the search box, filter by `kind:secret` to view all secrets.
     4. Within the list of all secrets, select the action menu for the secret that you want to delete. Click **Delete secret**.
     5. When the list of all secrets is refreshed, the secret is no longer displayed.

- To use the Kubernetes CLI tool to delete a secret, complete the following steps:
     1. Run the following command to delete the secret from a target namespace. Replace `name` and `namespace` with the name of your secret and your target namespace:
     ```
     kubectl delete secret <name> -n <namespace>
     ```
     
     2. Verify that your deployable is deleted by running the following command:
     ```
     kubectl get secrets <name>
     ```