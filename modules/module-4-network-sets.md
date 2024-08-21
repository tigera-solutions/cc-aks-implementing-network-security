# Module 4 - Ingress and Egress access control using NetworkSets

As you saw in the previous module, it is also possible to create network policies to control access to specific domain names. Now, let's use NetworkSets to compile a list of domain names, allowing the policy to refer to this list rather than to individual domains. NetworkSets are particularly useful when multiple policies require access to the same domains. Instead of incorporating the domains into each policy, you simply include them in the NetworkSet list, and it will automatically apply to all policies referencing it.

1. Let's start by creating the `allowed-api` NetworkSet by applying the following yaml.

   ```yaml
   kubectl apply -f - <<-EOF
   kind: NetworkSet
   apiVersion: projectcalico.org/v3
   metadata:
     name: allowed-api
     namespace: catfacts
     labels: 
       type: allowed-api
   spec:
     allowedEgressDomains:
     - 'dog.ceo'
     - 'catfact.ninja'
   EOF
   ```

   This NetworkSet is a list of two domains: `dog.ceo` and `catfact.ninja`.

2. Now, edit the `worker` policy to use a `NetworkSet` instead of inline DNS rule.

   Change the worker policy to refer to the allowed-api Network set using its label as `to:` `Endpoint`.

   ![netwkset](https://github.com/user-attachments/assets/21d60d6b-b97a-4a84-a151-c04b5fe521d8)

3. Test the access to the allowed endpoints.

   ```bash
   # test egress access to dog.ceo from worker pod - this should be allowed.
   kubectl -n catfacts exec -t $(kubectl -n catfacts get po -l app=worker -ojsonpath='{.items[0].metadata.name}') -- sh -c 'curl -m3 -skI https://dog.ceo 2>/dev/null | grep -i http'
   ```

   ```bash
   # test egress access to catfact.ninja from worker pod - this should be allowed.
   kubectl -n catfacts exec -t $(kubectl -n catfacts get po -l app=worker -ojsonpath='{.items[0].metadata.name}') -- sh -c 'curl -m3 -skI https://catfact.ninja/ 2>/dev/null | grep -i http'
   ```

4. Try to access an endpoint that was not allowed, like `api.twilio.com`, for example.

   ```bash
   # test egress access to api.twilio.com - this shoudl fail.
   kubectl -n catfacts exec -t $(kubectl -n catfacts get po -l app=worker -ojsonpath='{.items[0].metadata.name}') -- sh -c 'curl -m3 -skI https://api.twilio.com 2>/dev/null | grep HTTP'
   ```

5. Modify the `NetworkSet` to include `*.twilio.com` in dns domain and test egress access to `api.twilio.com` again.

   ```bash
   # test egress access to api.twilio.com again and it should be allowed.
   kubectl -n catfacts exec -t $(kubectl -n catfacts get po -l app=worker -ojsonpath='{.items[0].metadata.name}') -- sh -c 'curl -m3 -skI https://api.twilio.com 2>/dev/null | grep HTTP'
   ```

## Ingress Policies using NetworkSets

The NetworkSet can also be used to block access from a specific ip address or cidr to an endpoint in your cluster. To demonstrate it, we are going to block the access from your workstation to the `facts` external `LoadBalancer` service.

   a. Test the access to the `facts` external service

   ```bash
   curl -sI -m3 $(kubectl get svc -n catfacts facts -ojsonpath='{.status.loadBalancer.ingress[0].ip}') | grep -i http
   ```

   b. Identify your workstation ip address and store it in a environment variable

   ```bash
   export MY_IP=$(curl ifconfig.me)
   ```

   c. Create a NetworkSet with your ip address on it.

   ```yaml
   kubectl apply -f - <<-EOF
   kind: GlobalNetworkSet
   apiVersion: projectcalico.org/v3
   metadata:
     name: ip-address-list
     labels: 
       type: blocked-ips
   spec:
     nets:
     - $MY_IP/32
   EOF
   ```

   d. Create the policy to deny access to the `facts` service.

   ```yaml
   kubectl apply -f - <<-EOF
   apiVersion: projectcalico.org/v3
   kind: GlobalNetworkPolicy
   metadata:
     name: security.blockep-ips
   spec:
     tier: security
     selector: app == "facts"
     order: 300
     types:
       - Ingress
     ingress:
     - action: Deny
       source:
         selector: type == "blocked-ips"
       destination: {}
     - action: Pass
       source: {}
       destination: {}
   EOF
   ```

   e. Create a global alert for the blocked attempt from the ip-address-list to the frontend.

   ```yaml
   kubectl apply -f - <<-EOF   
   apiVersion: projectcalico.org/v3
   kind: GlobalAlert
   metadata:
     name: blocked-ips
   spec:
     description: "A connection attempt from a blocked ip address just happened."
     summary: "[blocked-ip] ${source_ip} from ${source_name_aggr} networkset attempted to access ${dest_namespace}/${dest_name_aggr}"
     severity: 100
     dataSet: flows
     period: 1m
     lookback: 1m
     query: '(source_name = "ip-address-list")'
     aggregateBy: [dest_namespace, dest_name_aggr, source_name_aggr, source_ip]
     field: num_flows
     metric: sum
     condition: gt
     threshold: 0
   EOF
   ```

   a. Test the access to the `facts` service. It is blocked now. Wait a few minutes and check the `Activity > Alerts`.

   ```bash
   curl -m3 $(kubectl get svc -n catfacts facts -ojsonpath='{.status.loadBalancer.ingress[0].ip}')
   ```

---

[:arrow_right: Module 5 - Application Level Observability](module-5-application-observability.md)  

[:arrow_left: Module 3 - Workload Isolation with Microsegmentation](module-3-wkload-isolation.md)  
[:leftwards_arrow_with_hook: Back to Main](../README.md)  
