# Streaming Event with Kafka

## Architecture
The core components of kafka are:
- `Brokers` : The dedicqted servers to receive, store, process and distribute events
- `Topics` : The containers or databases of events
- `Partitions`: Divide topics into different brokers
- `Replications`: Duplicate partitions into different brokers
- `Producers`: Kafka client applications that publish events into topics
- `Consumers`: Kafka client applications that subscribe to topics and read events.

There are 3 CLI managing different parts:
- `kafka-topics` CLI manages topics
- `kafka-console-producer` CLI manages producers
- `kafka-console-consumer` CLI manages consumers


## Example of a weather pipeline

```plantuml
@startuml
' Left-to-right direction
left to right direction
' Style settings for cleaner look
skinparam shadowing true
skinparam componentStyle rectangle
skinparam defaultFontSize 12
skinparam defaultTextAlignment center
skinparam wrapWidth 20

skinparam ArrowColor black
skinparam ArrowFontSize 10

skinparam databaseBackgroundColor #e6f0ff
skinparam databaseBorderColor #336699
skinparam queueBackgroundColor #fff7e6
skinparam queueBorderColor #ff9900
skinparam backgroundColor #ffffff

title Weather Data Streaming Flow Example

actor "IBM\nWeather\nCompany" as WeatherApi
component "Weather\nProducer" as WeatherProducer
queue "weather_topic" as WeatherTopic
component "Weather\nConsumer" as WeatherConsumer

actor "Twitter" as TwitterApi
component "Twitter\nProducer" as TwitterProducer
queue "twitter_topic" as TwitterTopic
component "Twitter\nConsumer" as TwitterConsumer

component "DB\nWriter" as DbWriter
database "RDBMS" as Rdbms
boundary "Weather\nDashboard" as WeatherDashboard

WeatherApi --> WeatherProducer : Weather\n events in\nJSON
WeatherProducer --> WeatherTopic : bytes
WeatherTopic --> WeatherConsumer : bytes
WeatherConsumer -->DbWriter

TwitterApi --> TwitterProducer : Tweets\nin\nJSON
TwitterProducer --> TwitterTopic : bytes
TwitterTopic --> TwitterConsumer : bytes
TwitterConsumer -->DbWriter

DbWriter --> Rdbms : records
Rdbms --> WeatherDashboard




@enduml
```


## Ad hoc weather stream processing

```plantuml
@startuml
' Left-to-right direction
left to right direction
' Style settings for cleaner look
skinparam shadowing true
skinparam componentStyle rectangle
skinparam defaultFontSize 12
skinparam defaultTextAlignment center
skinparam wrapWidth 20

skinparam ArrowColor black
skinparam ArrowFontSize 10

skinparam databaseBackgroundColor #e6f0ff
skinparam databaseBorderColor #336699
skinparam queueBackgroundColor #fff7e6
skinparam queueBorderColor #ff9900
skinparam backgroundColor #ffffff

title Ad hoc weather stream processing

actor "IBM\nWeather\nCompany" as WeatherApi
component "Weather\nProducer 1" as WeatherProducer1
queue "raw_weather_topic" as WeatherTopic
component "Weather\nConsumer 1" as WeatherConsumer1
component "Weather\nData\nProcessor" as WeatherDataProcessor

component "Weather\nProducer 2" as WeatherProducer2
queue "processed_weather_topic" as WeatherTopic2
component "Weather\nConsumer 2" as WeatherConsumer2

boundary "Weather\nDashboard" as WeatherDashboard

WeatherApi --> WeatherProducer1 : send
WeatherProducer1 --> WeatherTopic : write
WeatherTopic --> WeatherConsumer1 : read
WeatherConsumer1 -->WeatherDataProcessor: read

WeatherDataProcessor--> WeatherProducer2: send
WeatherProducer2--> WeatherTopic2: publish
WeatherTopic2--> WeatherConsumer2: read
WeatherConsumer2-> WeatherDashboard

@enduml
```

## Kafka Streams API

- A simple client library to facilitate data processing in event streaming pipelines.
- Processes and analyzes data stored in Kafka topics
- Each record only processed once
- Processing one record at a time

```plantuml
@startuml
' Left-to-right direction
left to right direction
' Style settings for cleaner look
skinparam shadowing true
skinparam componentStyle rectangle
skinparam defaultFontSize 12
skinparam defaultTextAlignment center
skinparam wrapWidth 20

skinparam ArrowColor black
skinparam ArrowFontSize 10

skinparam databaseBackgroundColor #e6f0ff
skinparam databaseBorderColor #336699
skinparam queueBackgroundColor #fff7e6
skinparam queueBorderColor #ff9900
skinparam backgroundColor #ffffff

title Ad hoc weather stream processing

rectangle "Kafka\nStreams\nAPI" as kAPI
rectangle "Apache Kafka" as AKafka

kAPI <-- AKafka : Raw streams
kAPI --> AKafka : Processed streams


@enduml
```

## Stream processing topology
 In this tipology, each node is a stream processor, which recceives from its upstream processor.


```plantuml
@startuml
' Left-to-right direction
left to right direction
' Style settings for cleaner look
skinparam shadowing true
skinparam componentStyle rectangle
skinparam defaultFontSize 12
skinparam defaultTextAlignment center
skinparam wrapWidth 20

skinparam ArrowColor black
skinparam ArrowFontSize 10

skinparam databaseBackgroundColor #e6f0ff
skinparam databaseBorderColor #336699
skinparam queueBackgroundColor #fff7e6
skinparam queueBorderColor #ff9900
skinparam backgroundColor #ffffff

title Stream processing topology

queue "topic" as topic1
queue "topic" as topic2

frame "Group 1" {
    component "Stream\nprocessor\n<Consume>" as strProc1_1
    component "Stream\nprocessor\n<Consume>" as strProc1_2
}
rectangle "Group 2" {
    component "Stream\nprocessor\n<Map>" as strProc2_1
    component "Stream\nprocessor\n<Filter>" as strProc2_2

}
frame "Group 3" {
    component "Stream\nprocessor\n<Aggregate>" as strProc3_1
    component "Stream\nprocessor\n<Format>" as strProc3_2
}
frame "Group 4" {
    component "Stream\nprocessor\n<Publish>" as strProc4_1
    component "Stream\nprocessor\n<Publish>" as strProc4_2
}



topic1 --> strProc1_1 : Stream
topic1 --> strProc1_2 : Stream

strProc1_1 --> strProc2_1 : Stream
strProc1_2 --> strProc2_2 : Stream

strProc2_1 --> strProc3_1 : Stream
strProc2_2 --> strProc3_2 : Stream




strProc3_1 --> strProc4_1 : Stream
strProc3_2 --> strProc4_2 : Stream

strProc4_1 --> topic2
strProc4_2 --> topic2

strProc2_1 -down-> strProc2_2 : Stream

@enduml
```