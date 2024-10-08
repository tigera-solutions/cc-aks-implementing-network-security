# Module 3 - Blocking Lateral Movement with Microsegmentation

Calico provides methods to enable fine-grained access controls between your microservices and external databases, cloud services, APIs, and other applications by using the workload access control with namespace isolation recommendation, as learned in the previous module.

If you need more restrictive policies, you can enforce controls on a fine-grained, per-pod basis using workload isolation with microsegmentation.

Let's use again the `Cat Facts Application` to demosntrate how to implement the microsegmentation by creating policies for each of the workloads, instead of creating a policy for the namespace.

![catfacts-application](https://github.com/tigera-solutions/cc-aks-zero-trust-workshop/assets/104035488/868c7ccf-e215-41d6-91ab-635832700c50)

## Security Policies

Calico Security Policies provide a richer set of policy capabilities than the native Kubernetes network policies, including:  

- Policies that can be applied to any endpoint: pods/containers, VMs, and/or to host interfaces
- Policies that can define rules that apply to ingress, egress, or both
- Policy rules support:
  - Actions: allow, deny, log, pass
  - Source and destination match criteria:
    - Ports: numbered, ports in a range, and Kubernetes named ports
    - Protocols: TCP, UDP, ICMP, SCTP, UDPlite, ICMPv6, protocol numbers (1-255)
    - HTTP attributes
    - ICMP attributes
    - IP version (IPv4, IPv6)
    - IP or CIDR
    - Endpoint selectors (using label expression to select pods, VMs, host interfaces, and/or network sets)
    - Namespace selectors
    - Service account selectors

### Creating the policies

1. Based on the application design, the `db` lists on port `3306` and receive connections from the `worker` and the `facts` microservices.
   Let's use the Calico Cloud UI to create a policy to microsegment this traffic.

   - Go to the `Policies Board`
   - On the bottom of the tier box `platform` click on `Add Policy`
     - In the `Create Policy` page enter the policy name: `db`
     - Change the `Scope` from `Global` to `Namespace` and select the namespace `catfacts`
     - On the `Applies To` session, click `Add Label Seletor`
       - Select Key... `app`
       - =
       - Select Value... `db`
       - Click on `SAVE LABEL SELECTOR`
     - On the field `Type` select the checkbox for Ingress.
     - Click on `Add Ingress Rule`
       - On the `Create New Policy Rule` window,
         - Click on the `dropdown` with `Any Protocol`
         - Change the radio button to `Protocol is` and select `TCP`
         - In the field `To:` click on `Add Port`
         - `Port is` 3306 - Save
         - In the field `From:`, click `Add Label Seletor`
           - Select Key... `app`
           - =
           - Select Value... `worker`
           - Click on `SAVE LABEL SELECTOR`  
           - On `OR`, click `Add Label Seletor`
           - Select Key... `app`
           - =
           - Select Value... `facts`
           - Click on `SAVE LABEL SELECTOR`
         - Click on the button `Save Rule`
     - You are done. Click `Enforce` on the top-right of your page.

2. Now, let's use the `Recommend a Policy` feature to create the policies for the other workloads.

   Let's start with the `facts` workload.
  
   ![recommend-policy](https://github.com/tigera-solutions/cc-aks-zero-trust-workshop/assets/104035488/00e7418c-b4e5-4564-95d3-8270912e19b6)
  
3. Now that you have learned how to create policies using Calico Cloud UI, go ahead and create microsegmentation policies for the `worker` workload.

> [!TIP]
>
> - Look into the Cat Facts application diagram to figure out all the communications to and from the `worker` microservice.
> - You can use the `Recommend a Policy` feature as well. Don't forget to edit the recommendations to make it as granular as possible, like restricting the domain names of the API's that the worker needs to communicate with (`dog.ceo` and `catfact.ninja`).

If you create all the policies correctly, at some point, you will start seeing zero traffic being denied by your default-deny staged policy. At that point, you can go ahead and enforce the default-deny policy. Voilà! The `catfacts` namespace is now secure.

> [!TIP]
>
> - After enforcing the default-deny policy, if you need to troubleshoot, use the Service Graph or the Flow Visualizations tools to see what traffic is being blocked.

### Bonus - About Tiers

Tiers are a hierarchical construct used to group policies and enforce higher-precedence policies that other teams cannot circumvent, providing the basis for **Identity-aware micro-segmentation**.

All Calico and Kubernetes security policies reside in tiers. You can start “thinking in tiers” by grouping your teams and the types of policies within each group, such as security, platform, etc.

Policies are processed in sequential order from top to bottom.

![policy-processing](https://user-images.githubusercontent.com/104035488/206433417-0d186664-1514-41cc-80d2-17ed0d20a2f4.png)

Two mechanisms drive how traffic is processed across tiered policies:

- Labels and selectors
- Policy action rules

For more information about tiers, please refer to the Calico Cloud documentation [Understanding policy tiers](https://docs.calicocloud.io/get-started/tutorials/policy-tiers)

---

[:arrow_right: Module 4 - Ingress and Egress access control using NetworkSets](module-4-network-sets.md)  

[:arrow_left: Module 2 - Zero-Trust Workload Access Control with Namespace Isolation Recommendation](module-2-ztac-ns-isolation.md)  
[:leftwards_arrow_with_hook: Back to Main](../README.md)  
