# CAM alerting using TR- Logging

### This document gives a brief about how to raise and manage alarms pusblishing to the TR-Logging ingest topics

CAM dashboard alarms from TR Logging Schema compatible events.
Alarming events: "sp-isAlarm": true,
Turns alarming event json into an alarming episode:
Image 1

## How does it work?
Image 2
- Application sends TR Logging Schema based Alarming Event.
- Alarming events: "sp-isAlarm": true -> translates event to alarms
- Alarm should be available in TRAMS ELK .
- Compass Event Collector [details](k=close)
- To implement TR- Logging refer [TR-Logging Project ](g-Project)
- For CAM details refer [TR-Alarm Management](ogModel)

## *TR Logging Extensions ([Example](oject-example)) – HowTo Steps*
1. Register the application in the Software Module Registry
    * Pre-requisite: sp-applicationUniqueId – Asset Insight Unique ID
    * More info : [Software Module Registration](ter/README.md)
2. Include necessary internal TR Enterprise Logging and third-party libraries in the application build path
3. Application logging configuration: set up TR Enterprise Logging Kafka  appender (e.g. EnterpriseKafkaLog4jAppender) and JSON event layout (e.g. Log4jJsonEventLayout)
4. Add the necessary JVM parameters to the application’s startup or put required the properties in application code. 
5. Add logging logic to application code 
6. Log events will show up in Kibana dashboard, Alarm events will show up in CAM.[CAM Dashboard]



#### Include internal TR Enterprise Logging and third-party libraries in the application build 

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
http://coba


#### Application Logging Configuration 
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

#### SM Registry properties specified as VM argument
-Dsp-eventSourceUUID=4800a410-81f9-476e-9dd0-ce8fd71ccd2a </br>
-Dsoftware.module.registry.service.url="trams-logstash-eag-preprod.int.thomsonreuters.com:9200"  </br>
-Denvironment=demo -DinstantiateRegistryUtil=true</br>
-Denvironment=qed </br>

---image 2

---image 3
[[https://github.com/Sabya2015/MyLoggingDoc/blob/master/img/event]]


### Example - the resulting alarm
image -4

[Mandatory and optional fields](https7) 
[Mapping rules in place](http)

Alarm correlation: * For grouping alarms to episodes. *
- Group related alarms together: alarming episodes
- Alarm Correlation Signature:

#### correlation_signature 
Correlation id which identifies an episode as a unique entity
```javascript 
1 (if provided) sp-softwareModuleName +
2 sp-eventContext.sp-environmentClass +
3 (if provided) sp-eventContext.sp-environmentLabel +
4 (if provided) sp-eventContext.sp-hostingModuleID  +
5 sp-message OR (if provided) sp-eventGroupID )
```

#### Alarm 1

##### * Message *
```javascript
 {                    
  "sp-isAlarm": true,
  "sp-applicationUniqueID": "202564",
  "sp-eventSourceUUID": "30044842-21c9-11e6-b67b-9e71128cae77",
  "sp-timestamp": "2016-11-21T13:50:40.555Z",
  "sp-eventSchemaVersion": 3,
  "sp-message":"HelloWorld-TR",
  "sp-eventSeverity": "warning",
    "sp-eventContext": {  
    "sp-environmentClass": "pre-production"
  }
}




correlation_signature =
1 ( +
2 "pre-production" +
3  +
4  +
5  "HelloWorld-TR" )
 

Result

"correlation_id":"pre-productionHelloWorld-TR"
```


#### Alarm 2
##### *Message*
```javascript
 {
  "sp-isAlarm": true,
  "sp-applicationUniqueID": "202564",
  "sp-eventSourceUUID": "30044842-21c9-11e6-b67b-9e71128cae77",
  "sp-timestamp": "2016-11-21T13:52:30.555Z",
  "sp-eventSchemaVersion": 3,
  "sp-softwareModuleName": "HelloWorldTRLoggingTest", 
  "sp-message":"HelloWorld-TR",
  "sp-eventSeverity": "warning",
  "sp-eventContext": {  
  "sp-environmentClass": "pre-production"
  }
}

correlation_signature =
1 ("HelloWorldTRLoggingTest" +
2 "pre-production" +
3  +
4  +
5  "HelloWorld-TR" )

Result

"correlation_id":
"HelloWorldTRLoggingTestpre-productionHelloWorld-TR"
```
image 5, image 6










## Troubleshooting with Postman – [details](http97)
-image 7
-image 8

#### Using Postman to verify CAM alarming event 

POST </br>
http:vents 


Headers
 - Content-Type application/json

Body
 - Raw

##### *Example on screenshot:*
 ```javascript
{                                                      
  "sp-isAlarm": true,
  "sp-applicationUniqueID": "202564",
  "sp-eventSourceUUID": "30044842-21c9-11e6-b67b-9e71128cae77",
  "sp-timestamp": "2016-10-26T13:53:30.555Z",
  "sp-eventSchemaVersion": 3,
  "sp-softwareModuleName": "HelloWorldTRLoggingTest", 
  "sp-message":"HelloWorld-TR",
  "sp-eventSeverity": "warning",
  "sp-eventContext": {  
    "sp-environmentClass": "pre-production",
    "sp-eventSourceHostname": "c123abc.int.thomsonreuters.com"
  }
}
 ```

### *Clearing alarms*

- You can auto-clear alarms via application logging
- You can manually clear alarms from Dashboard
- Alarms are archived (disappearing from live dashboard)
  - Critical 4 days after last update
  - Warning 2 days after last update
  - OK 1day after last update
- Archived alarms are kept 6 months
- You can access archived alarms from Reporting tool


#### Auto CLear
To auto clear a previous warning/critical alarm, you must send an alarm with OK severity and same correlation id ( as critical/warning) driven from a number of fields as described in Correlation id.

#### To clear alarm manually:
It does not have to be a manual process. However, the dashboard allows you to take ownership of an alarm and change severity to OK.

[Clearing alarm details](htlarms)</br>
image -7


#### *How to check Alarms after sending event from TR Logging Extensions*
1) Go to [CAM dashboard](htrms/)

image -8
compasDashboard.png

2) Click on the Show Filters img at top left corner
3) Click on Default tab and clear all the filters for default

image -9
compasClearFilter.png
4) Click on Application tab and type in your application name and enter.
Hover on + symbol of SSDF and click on the event type(critical in this case)
img -10

5) Will get the application and count of events based on level.
Click on the aplication 
img -11
6) GIves further details about the perticular event with details .
img -12





### References

[The Thomson Reuters Enterprise Logging Framework](htProject)</br>
[CAM dashboard](httrms/)</br>
[CAM alarm from TRLogModel](http7)</br>
[Developers Guide](httdit) </br>

##### TR Logging-Compass Monitor Integration </br>
htt1850624

##### FAQs </br>
http79951
