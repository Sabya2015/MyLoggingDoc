# MyLoggingDoc

Image 1

## How does it work?
- Application sends TR Logging Schema based Alarming Event.
- Alarming events: "sp-isAlarm": true -> translates event to alarms
- Alarm should be available in TRAMS ELK .
- Compass Event Collector [details]()
- To implement TR- Logging refer [TR-Logging Project]()
- For CAM details refer [TR-Alarm Management]()

## *TR Logging Extensions ([Example]()) – HowTo Steps*
1. Register the application in the Software Module Registry
    * Pre-requisite: sp-applicationUniqueId – Asset Insight Unique ID
    * More info : [Software Module Registration]()
2. Include necessary internal TR Enterprise Logging and third-party libraries in the application build path
3. Application logging configuration: set up TR Enterprise Logging Kafka  appender (e.g. EnterpriseKafkaLog4jAppender) and JSON event layout (e.g. Log4jJsonEventLayout)
4. Add the necessary JVM parameters to the application’s startup or put required the properties in application code. 
5. Add logging logic to application code 
6. Log events will show up in Kibana dashboard, Alarm events will show up in CAM.[CAM Dashboard]



#### Include internal TR Enterprise Logging and third-party libraries in the application build (Example - step 2)

### *Dependencies (Gradle Format)*

#### Internal Libraries
    - compile 'com.trgr.cobalt.infrastructure.ServiceRegistry:ServiceRegistry:34.0.4'
    - compile 'com.trgr.cobalt.infrastructure.KafkaMessagingUtil:KafkaMessagingUtil:32.3.12'
    - com.trgr.cobalt.infrastructure.loglayout:loglayout:34.0.1'

#### External Libraries
    - compile 'org.slf4j:slf4j-api:1.7.21'
    - compile 'org.json:json:20160212'
    - compile 'org.apache.kafka:kafka_2.10:0.8.2.1'
    - compile 'org.apache.kafka:kafka-clients:0.8.2.1'
    - compile 'org.scala-lang:scala-library:2.10.4'

#### All libaraies available internally from: 
http://cobaltdm

#### Sami-bams coming soon:
https://bams


#### Application Logging Configuration (Example - step 3)
```javascript 
 <appender name="KafkaLog4jAppender" class="com.thomsonreuters.enterpriselogging.appender.log4j.EnterpriseKafkaLog4jAppender">  
       <param name="brokerList" value="kafka-bold-qed.int.thomsonreuters.com:9092" />
       <param name="requiredNumAcks" value="0" />
       <param name="compressionType" value="none" />
       <param name="topic" value="event.aggregation.default" />
       <param name="alertTopic" value="event.aggregation.priority" />
       <param name="syncSend" value="true" />
       <param name="headerNames" value="sp-timestamp,sp-eventSourceUUID,sp-eventType,sp-eventSeverity,sp-isAlarm" />
       <layout class="com.thomsonreuters.enterpriselogging.log4j.Log4jJsonEventLayout" />
  </appender>    
```

#### JVM Args (Example - step 4)
-Dsp-eventSourceUUID=4800a410-81f9-476e-9dd0-ce8fd71ccd2a -Dsoftware.module.registry.service.url="trams-logstash-eag-preprod.int.thomsonreuters.com:9200" -Denvironment=demo -DinstantiateRegistryUtil=true


#### SM Registry properties specified as VM argument
-Dsp-eventSourceUUID=4800a410-81f9-476e-9dd0-ce8fd71ccd2a 
-Dsoftware.module.registry.service.url="trams-logstash-eag-preprod.int.thomsonreuters.com:9200" 
-Denvironment=demo -DinstantiateRegistryUtil=true
-Denvironment=qed

---image 2

---image 3
[[https://github.com/Sabya2015/MyLoggingDoc/blob/master/img/event]]

