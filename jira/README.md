# Prometheus Plugin Integration 
######  Manual
- You can download the prometheus plugin jar from 
        [https://marketplace.atlassian.com/apps/1217960/prometheus-exporter-for-jira?hosting=server&tab=overview ]
- Drop above jar at plugin directoty  
        `</var/atlassian/application-data/jira/plugins/installed-plugins or /var/atlassian/jira/plugins/installed-plugins>` .
- You can verify prometheus plugin by - Open Jira's plug-in manager, and in the Find new add-ons section ypu will find prometheus exporter section.
- You can configure secret token to allow access only by token. On Jira Administration > Add-Ons locate and click Prometheus Exporter Settings. In opened window specify secret token. 
- The scrape URL will be then: /plugins/servlet/prometheus/metrics?token=secrettoken. 


###### Packaging in Image
 - To pack the plugin in image you can pass the plugin jar as dependent file .
 - And mention the jar name and directory name where the jar need to get place
     - `ex : "prom-jira-exporter-1.0.21.jar":"/var/atlassian/jira/plugins/installed-plugins"`
 - Then you can create image , it will come up with the prometheus plugin.
  

# ServiceMonitor Creation 
###### Manual
- Create servicemonitor yaml file yaml (jiraervicemonitor.yaml) auto-detect target pods based on the label selectors and associate them with the Prometheus instances.  
- Edit the file and add the below details 
   ![jiraservice](/uploads/58751a1e807d6d39f4de3905b8113848/jiraservice.PNG)
- That’s it. Now we just need to apply these files to our cluster. 
       - ` kubectl apply -f jiraservicemonitor.yaml -n nameofnamespace `
   - The result you can verify by using the below command 
       - ` kubectl get servicemonitor -n nameofnamespace `

###### Using Prometheus-Operator  
   - You can just edit the prometheus-operator yaml file under that you just need to insert the below service monitor details 
     ![Capture](/uploads/42d07924022174306a9ad77d04072c45/Capture.PNG)
   - Restart your prometheus pod. 

# Scrap Configuartion 
###### Target Port & Path
  - scrap config
       - job_name: `'jiraexporter'`
       - metrics_path: `'/plugins/servlet/prometheus/metrics'`
       - static_configs:
           - targets: `['hostname:port']`
       - params:
           -  token: `['xxxxxxxxxxxxxxxx']` 


# Apply Label & ServiceMonitor selector
 - The ServiceMonitor has a label selector to select Services and their underlying Endpoint objects.
 - The Service object for the example application selects the Pods by the app label having the example-app value. 
 - This Service object is discovered by a ServiceMonitor, which selects in the same way. The app label must have the value example-app.
 - In our case :
    - `kubectl label svc -n nameofnamespace jira app=jira release=prometheus-operator`

# Testing
-  Go to the prometheus page and under Status> Configuration , you will find the scrap config if not found , then restart the prometheus operator pod
    - `kubectl scale --replicas=1 deployment tooldeploymentname -n namespacename `
-  Also verify by accessing the metric path : `<http://jira-svc:nodeport/plugins/servlet/prometheus/metrics?token=secrettoken>`
-  You can then access the Prometheus Target in your browser entering this address Status> Targets

# Dashboard 
###### Connect Prometheus with Grafana:
   - Go to Grafana server `<http://grafana-svc:nodeport>` and login with username and password.
   - From “ Home Dashboard” click on “add data source” then click on “Prometheus”.
     ![database](/uploads/3af4ff833ff9f9929e819ba37e0d1774/database.PNG)
   - In the URL field enter Prometheus URL in our case `<http://grafana-svc:nodeport>`. you can leave all other fields to default. Then click save and test.
   - Now you are ready to create your dashboard.

###### Create a dashboard in Grafana to display data:
  - Now you have a dashboard that represent the piece of information about Jira. 
  - You can add as many panels as you want to create your own dashboard.
     
![Capture](/uploads/106d8f2aeca27263c3d75f27795af918/Capture.PNG)
 






    