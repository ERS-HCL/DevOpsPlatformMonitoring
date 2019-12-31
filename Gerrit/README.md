# Prometheus Plugin Integration 
######  Manual
- You can download the prometheus plugin jar from 
        [https://gerrit-ci.gerritforge.com/ ]
- Drop above jar at plugin directoty  
        `/</var/gerrit/review_site/plugins/>` and restart the gerrit service.
- You can verify prometheus plugin by admin web interface ,go  to Plugins>Installed to verify that metrics-reporter-prometheus is listed and enabled.
- Create a account and group to to give access capability to view metrics
   - Log in to Gerrit web interface with an account with admin privileges, go to Projects>List>All-Projects 
   - Click on the tab 'Access'
   - Click on the button [EDIT]
   - In the block Global Capabilities, click on Add Permission (that's typically the 4th drop down menu)
      - Select the permission View Metrics in the drop-down list
      - Select the group "Prometheus Metrics" we created above.
      - Click on the button [Add]
      - Go to the bottom of the page and click on the [Save Changes] button
    ![12](/uploads/9cccd9edea307b5bda5b242b851e1510/12.PNG)

###### Packaging in Image
 - To pack the plugin in image you can pass the plugin jar as dependent file .
 - And mention the jar name and directory name where the jar need to get place
     - `ex : "metrics-reporter-prometheus.jar":"/var/gerrit/review_site/plugins/",`
 - Then you can create image , it will come up with the prometheus plugin.
  

# ServiceMonitor Creation 
###### Manual

 - Create Kubernetes Secrets (secrete-gerrit.yaml)file 
 - Edit the file and add the below details .

![1234](/uploads/071dab637b5e8a548011b784988679fb/1234.PNG)

- Create servicemonitor yaml file yaml (gerritservicemonitor.yaml) auto-detect target pods based on the label selectors and associate them with the Prometheus instances.  
- Edit the servicemonitor file and add a jobas below shown :

![gerritservicemonitor](/uploads/131e8d699223c8a991bc92e5e287317d/gerritservicemonitor.PNG)
            

   - That’s it. Now we just need to apply these files to our cluster. 
       - ` kubectl apply -f secrete-gerrit.yaml -n nameofnamespace `
       - ` kubectl apply -f gerritservicemonitor.yaml -n nameofnamespace `
   - The result you can verify by using the below command 
       - ` kubectl get servicemonitor -n nameofnamespace `


# Apply Label & ServiceMonitor selector
 - The ServiceMonitor has a label selector to select Services and their underlying Endpoint objects.
 - The Service object for the example application selects the Pods by the app label having the example-app value. 
 - This Service object is discovered by a ServiceMonitor, which selects in the same way. The app label must have the value example-app.
 - In our case :
    - `kubectl label svc -n nameofnamespace gerrit app=gerrit release=prometheus-operator`




###### Using Prometheus-Operator  
   - You can just edit the prometheus-operator yaml file under that you just need to insert the below service monitor details 
![operatorservicemonitor](/uploads/9e5b2e25fdddcc840945b03b9be5279c/operatorservicemonitor.PNG)
   - Restart your prometheus pod. 

# Scrap Configuartion 
###### Target Port & Path
  - scrap config
       - job_name: `'gerritexporter'`
       - metrics_path: `'/a/plugins/metrics-reporter-prometheus/metrics'`
       - static_configs:
           - targets: `['hostname:port']`
       - basic_auth:
           - username: `username`
           - password: `xxxxgeneratedpasswordxxxx`
          

# Testing
-  Go to the prometheus page and under Status> Configuration , you will find the scrap config if not found , then restart the prometheus operator pod
    - `kubectl scale --replicas=1 deployment tooldeploymentname -n namespacename `
-  Also verify by accessing the metric path : `<http://gerrit-svc:nodeport/a/plugins/metrics-reporter-prometheus/metrics/>`
-  You can then access the Prometheus Target in your browser entering this address Status> Targets

![taget](/uploads/bcaade5854b2851d031acdb4f26cb652/taget.PNG)

# Dashboard 
###### Connect Prometheus with Grafana:
   - Go to Grafana server `<http://grafana-svc:nodeport>` and login with username and password.
   - From “ Home Dashboard” click on “add data source” then click on “Prometheus”.
     ![database](/uploads/3af4ff833ff9f9929e819ba37e0d1774/database.PNG)
   - In the URL field enter Prometheus URL in our case `<http://grafana-svc:nodeport>`. you can leave all other fields to default. Then click save and test.
   - Now you are ready to create your dashboard.

###### Create a dashboard in Grafana to display data:

 ![gerritdevopsmonitoring](/uploads/aa57b737fae5c9f98155af69f1e4ee16/gerritdevopsmonitoring.PNG) 





    