# Memory usage policy 

Kubernetes configuration policy controller monitors the status of the memory usage policy. Use the memory usage policy to limit or restrict your memory and compute usage. For more information, see _Limit Ranges_ in the [Kubernetes documentation](https://kubernetes.io/docs/concepts/policy/limit-range/). Learn more details about the memory usage policy structure in the following sections.

## Memory usage policy YAML structure

Your memory usage policy might resemble the following YAML file:

   ```yaml
   apiVersion: policy.mcm.ibm.com/v1alpha1
   kind: Policy
   metadata:
     name: policy-limitrange
     namespace:
   spec:
     complianceType:
     remediationAction:
     namespaces:
       exclude:
       include:
     object-templates:
       - complianceType:
         objectDefinition:
           apiVersion:
           kind:
           metadata:
             name:
           spec:
             limits:
             - default:
                 memory:
               defaultRequest:
                 memory:
               type: 
           ...
   ```

## Memory policy table
<!--need to come back and revise according to the memory usage policy; currently a place holder-->

|Field|Description|
|-- | -- |
| apiVersion | Required. Set the value to `policy.mcm.ibm.com/v1alpha1`. <!--current place holder until this info is updated--> |
| kind | Required. Set the value to `Policy` to indicate the type of policy. |
| metadata.name | Required. The name for identifying the policy resource. |
| metadata.namespaces | Optional. |
| spec.namespace | Required. The namespaces within the hub cluster that the policy is applied to. Enter parameter values for `include`, which are the namespaces you want to apply to the policy to. The `exclude` parameter specifies the namespaces you explicitly do not want to apply the policy to. **Note**: A namespace that is specified in the object template of a policy controller overrides the namespace in the corresponding parent policy.|
| remediationAction | Optional. Specifies the remediation of your policy. The parameter values are `enforce` and `inform`. **Important**: Some policies might not support the enforce feature.|
| disabled | Required. Set the value to `true` or `false`. The `disabled` parameter provides the ability to enable and disable your policies.|
| spec.complianceType | Required. Set the value to `"musthave"`|
| spec.object-template| Optional. Used to list any other Kubernetes object that must be evaluated or applied to the managed clusters. |
{: caption="Table 1. Required and optional definition fields" caption-side="top"}

## Memory policy sample

   ```yaml
   apiVersion: policy.mcm.ibm.com/v1alpha1
   kind: Policy
   metadata:
     name: policy-limitrange
     namespace: mcm 
   spec:
     complianceType: musthave
     remediationAction: inform
     namespaces:
       exclude: ["kube-*"]
       include: ["default"]
     object-templates:
       - complianceType: musthave
         objectDefinition:
           apiVersion: v1
           kind: LimitRange # limit memory usage
           metadata:
             name: mem-limit-range
           spec:
             limits:
             - default:
                 memory: 512Mi
               defaultRequest:
                 memory: 256Mi
               type: Container
           ...
   ```

See [Managing memory usage policies](create_memory_policy.md) for more information. View other configuration policies that are monitored by controller, see the [Kubernetes configuration policy controller](config_policy_ctrl.md) page.

