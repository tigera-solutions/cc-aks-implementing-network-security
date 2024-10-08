# Module 8 - Clean up

1. Delete the applications stack to clean up any `loadbalancer` services.

   ```bash
   kubectl delete -f pre/40-catfacts-app.yaml
   ```

2. Delete the AKS cluster.

   ```bash
   az aks delete \
     --resource-group $RESOURCE_GROUP \
     --name $CLUSTERNAME
   ```

3. Delete the resource group.

   ```bash
   az group delete \
     --name $RESOURCE_GROUP
   ```

4. Delete environment variables backup file.

   ```bash
   rm ~/workshopvars.env
   ```

5. Delete this cloned repository

   ```bash
   cd .. && rm -Rf cc-aks-implementing-network-security
   ```

---

[:arrow_left: Module 5 - Application Level Observability](module-5-application-observability.md)  
[:leftwards_arrow_with_hook: Back to Main](../README.md)  
