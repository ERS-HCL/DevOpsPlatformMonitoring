# Prometheus Plugin Integration 
######  Manual
- To enable prometheus in gitlab you can deploy base image then edit the yaml file with below yellow marked 
   Environment:
      GITLAB_OMNIBUS_CONFIG

![gitlabpromtheus](/uploads/963f1c82f9390bcc5040f120122bdee3/gitlabpromtheus.PNG)
###### Packaging in Image
 
  

# ServiceMonitor Creation 
###### Manual
- Create servicemonitor yaml file yaml (gitlabservicemonitor.yaml) auto-detect target pods based on the label selectors and associate them with the Prometheus instances.  
- Edit the file and add the below details 
   
- That’s it. Now we just need to apply these files to our cluster. 
       - ` kubectl apply -f gitlabaservicemonitor.yaml -n nameofnamespace `
   - The result you can verify by using the below command 
       - ` kubectl get servicemonitor -n nameofnamespace `

###### Using Prometheus-Operator  
   - You can just edit the prometheus-operator yaml file under that you just need to insert the below service monitor details 
     ![Capture](/uploads/42d07924022174306a9ad77d04072c45/Capture.PNG)
   - Restart your prometheus pod. 

# Scrap Configuartion 
###### Target Port & Path
  - scrap config
       - job_name: `'Gitlabexporter'`
       - metrics_path: `'/-/metrics'`
       - static_configs:
           - targets: `['hostname:port','']`
       - params:
           -  token: `['xxxxxxxxxxxxxxxx']` 
           

# Apply Label & ServiceMonitor selector
 - The ServiceMonitor has a label selector to select Services and their underlying Endpoint objects.
 - The Service object for the example application selects the Pods by the app label having the example-app value. 
 - This Service object is discovered by a ServiceMonitor, which selects in the same way. The app label must have the value example-app.
 - In our case :
    - `kubectl label svc -n nameofnamespace gitlab app=gitlab release=prometheus-operator`


# Testing
-  Go to the prometheus page and under Status> Configuration , you will find the scrap config if not found , then restart the prometheus operator pod
    - `kubectl scale --replicas=1 deployment tooldeploymentname -n namespacename `
-  Also verify by accessing the metric path : `<http://gitlab-svc:nodeport/-/metrics>`
-  You can then access the Prometheus Target in your browser entering this address Status> Targets
    ![Capture1](/uploads/3992fbbc77599a7111dc400cf98b7a0b/Capture1.PNG)

# Dashboard 
###### Connect Prometheus with Grafana:
   - Go to Grafana server `<http://grafana-svc:nodeport>` and login with username and password.
   - From “ Home Dashboard” click on “add data source” then click on “Prometheus”.
     ![database](/uploads/3af4ff833ff9f9929e819ba37e0d1774/database.PNG)
   - In the URL field enter Prometheus URL in our case `<http://grafana-svc:nodeport>`. you can leave all other fields to default.
   - Then click save and test.
   - Now you are ready to create your dashboard.

###### Create a dashboard in Grafana to display data:
  - Now you have a dashboard that represent the piece of information about Gitlab. 
  - You can add as many panels as you want to create your own dashboard.
     ![Capture](/uploads/58d30d42920aa2a89fc5f0ffb3b83b1d/Capture.PNG)







    