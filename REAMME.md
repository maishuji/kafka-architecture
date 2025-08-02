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