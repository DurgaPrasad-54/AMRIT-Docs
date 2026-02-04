# Observability



Observability in AMRIT aims to implement a comprehensive solution that provides a unified view of application logs and metrics, thereby automating error detection and troubleshooting processes. This initiative aims to significantly reduce the time spent on manual monitoring, enhance incident response times, and minimize system downtime by leveraging real-time data visualization and advanced analytics.

We adopted the ELK stack to achieve the above objective.

**Setting up ELK on Ubuntu:**

1. Install and configure ELK on UBUNTU as a single node cluster.
2.  **Check Elasticsearch:**

    * Test Elasticsearch by accessing its URL in a browser:  curl -X GET "localhost:9200"
    * If Elasticsearch is running correctly, you should see a JSON response with Elasticsearch cluster details.

    **Check Kibana:**

    * Go to Kibana’s dashboard by accessing its URL in a browser [http://localhost:5601](http://localhost:5601/)
3. Copy the [apm\_agent](https://github.com/PSMRI/AMRIT-DevOps/tree/main/ELK/apm_agent) folder to the server (preferably in the Wildfly installation location)
4.  **apm\_agent** folder contains two files **elastic-apm-agent.jar, elasticapm.properties**&#x20;

    **Add the below lines in elasticapm.properties**

    server\_url=http://\<private-ip>:8200

    enable\_log\_correlation=true

    environment=hyd-sandbox
5. **Filebeat Setup on Application Nodes**

* Filebeat is used to forward logs from our application nodes to Logstash or Elasticsearch.
* Download and [install ](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-installation-configuration.html)
* Download [http\_ca.crt](https://github.com/PSMRI/AMRIT-DevOps/blob/main/ELK/http_ca.crt) to the server (preferably in the filebeat installation location)
* Create a Logs folder inside Wildfly&#x20;
* Do the changes in Filebeat.yml as mentioned [here](https://github.com/PSMRI/AMRIT-DevOps/blob/main/ELK/filebeat.yml)&#x20;

**Switch to Wildfly/bin/ and edit standalone.conf.bat add below line under wildfly folder**

set "JAVA\_OPTS=%JAVA\_OPTS% -javaagent:E:\\\AppServer\\\wildfly-30.0.0.Final\\\wildfly-30.0.0.Final\\\apm\_agent\\\elastic-apm-agent-1.52.0.jar"

restart wildfly&#x20;

check if traces are visible in APM section of kibana &#x20;

restart filebeat

check for logs under filebeat in kibana&#x20;

**Do the changes in github repo under ci-properties & application properties for each service line.**

**Example:**

Goto [common-ci.properties](https://github.com/PSMRI/HWC-API/blob/develop/src/main/environment/common_ci.properties)

ADD a new line

[logging.file.name=@env.HWC\_API\_LOGGING\_FILE\_NAME@](mailto:logging.file.name=@env.HWC_API_LOGGING_FILE_NAME@)&#x20;

Goto [application.properties](https://github.com/PSMRI/HWC-API/blob/develop/src/main/resources/application.properties)&#x20;

Add below two lines:

logging. Path=logs/&#x20;

[logging.file.name](http://logging.file.name/)=logs/hwc-api.log

**4.1. Define Environment Variables in Jenkins Pipeline:**

In the Jenkins pipeline, set the environment variable

env.HWC\_API\_LOGGING\_FILE\_NAME='E:/AppServer/wildfly-30.0.0.Final/wildfly-30.0.0.Final/Logs/hwc-api.log'            &#x20;

**5.4. Check Application Logs:**

Check if the logs are being generated at the specified location (e.g., logs/inventory-api.log) and whether they are being processed by Filebeat and appear in Kibana.

**Check Kibana:**

* Go to Kibana’s dashboard by accessing its URL in a browser [http://localhost:5601](http://localhost:5601/)
* Navigate to the "Discover" section and check if logs are being indexed in Elasticsearch.

**Troubleshooting**

* **Filebeat not sending logs:** Check the Filebeat logs at /var/log/filebeat/ for errors.
* **Elasticsearch or Kibana not working:** Review the logs in /var/log/elasticsearch/ and /var/log/kibana/.
* **Application not writing logs:** Ensure that the application has access to the specified log path and that the environment variable is being correctly passed from Jenkins.

&#x20;![](https://pmp.piramalswasthya.org/confluence/download/attachments/64782362/image-2024-12-9_14-41-27-1.png?version=1\&modificationDate=1733735488021\&api=v2)

![](https://pmp.piramalswasthya.org/confluence/download/attachments/64782362/image-2024-12-9_14-41-47-1.png?version=1\&modificationDate=1733735507125\&api=v2)

\
<img src="../.gitbook/assets/image (1).png" alt="" data-size="original">

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

**Conclusion**

This document outlines the process of configuring ELK for log collection from our application. We have set up Elasticsearch, Logstash, and Kibana on Ubuntu, configured Filebeat for log forwarding, and adjusted our application’s properties file to use environment variables for log paths. The Jenkins pipeline is configured to pass the necessary environment variables to the application, ensuring that the logs are written to the correct location.

<br>
