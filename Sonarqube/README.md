# Prometheus Plugin Integration 
######  Manual
- Download latest snapshot release sonar-prometheus-exporter-1.0.0-SNAPSHOT.jar into $SONARQUBE_HOME/extensions/plugins.
- Restart the SonarQube server.
- This plugin will expose an endpoint (default /prometheus) with metrics where a Prometheus Server can scrape data.
- You can see the output of this plugin visiting this URL
     - `http://SONARQUBE_HOST:PORT/prometheus`

###### Packaging in Image
 - To pack the plugin in image you can pass the plugin as dependent file .
 - And mention the jar name and directory name where the jar need to get place
     ex : "sonar-prometheus-exporter-1.0.0-SNAPSHOT.jar":SONARQUBE_HOME/extensions/plugins",
 - Then you can create image , it will come up with the prometheus plugin.
  

# ServiceMonitor Creation 
###### Manual
- Create servicemonitor yaml file yaml (sonarservicemonitor.yaml) auto-detect target pods based on the label selectors and associate them with the Prometheus instances.  
- Edit the servicemonitor file and add a job as shown below
   ![1234](/uploads/8f317f71be750758c751cb580126cb0d/1234.PNG)
- That’s it. Now we just need to apply these files to our cluster.
       - ` kubectl apply -f sonarservicemonitor.yaml -n nameofnamespace `
- The result you can verify by using the below command 
       - ` kubectl get servicemonitor -n nameofnamespace `

###### Using Prometheus-Operator  
   - You can just edit the prometheus-operator yaml file under that you just need to insert the below service monitor details 
    ![servicemonitorjenkins](/uploads/f66f13d476b4f2cbc6594a1c24ad2146/servicemonitorjenkins.PNG)
   - Restart your prometheus pod. 

      
# Apply Label & ServiceMonitor selector
 - The ServiceMonitor has a label selector to select Services and their underlying Endpoint objects.
 - The Service object for the example application selects the Pods by the app label having the example-app value. 
 - This Service object is discovered by a ServiceMonitor, which selects in the same way. The app label must have the value example-app.
 - In our case :
    - `kubectl label svc -n nameofnamespace jenkins app=sonarqube release=prometheus-operator`



# Scrap Configuartion 
###### Target Port & Path
  - scrap configs
       - job_name: `'sonarqube'`
       - metrics_path: `'/api/prometheus/metrics'`
       - static_configs:
           - targets: `['hostname:port']`
  
# Testing
-  Go to the prometheus page and under Status> Configuration , you will find the scrap config if not found , then restart the prometheus operator pod
    - `kubectl scale --replicas=1 deployment tooldeploymentname -n namespacename `
-  Also verify by accessing the metric path : `<http://jenkins-svc:nodeport/prometheus>`
-  You can then access the Prometheus Target in your browser entering this address Status> Targets
   ![target_jenkins](/uploads/9187cb686165c9bc9a5c1f7f8b01a86c/target_jenkins.PNG)

# Dashboard 
###### Connect Prometheus with Grafana:
   - Go to Grafana server `<http://grafana-svc:nodeport>` and login with username and password.
   - From “ Home Dashboard” click on “add data source” then click on “Prometheus”.
     ![database](/uploads/3af4ff833ff9f9929e819ba37e0d1774/database.PNG)
   - In the URL field enter Prometheus URL in our case `<http://grafana-svc:nodeport>`. you can leave all other fields to default. Then click save and test.
   - Now you are ready to create your dashboard.

###### Create a dashboard in Grafana to display data:
  - Now you have a dashboard that represent the piece of information about Jenkins. 
  - You can add as many panels as you want to create your own dashboard.
 
   ![jenkinsdevopsmonitoring](/uploads/8ee963d496d79edc5e4451d803135ff0/jenkinsdevopsmonitoring.PNG)
   




    