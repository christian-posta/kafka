#### New Consumer API

This new unified consumer API removes the distinction between the 0.8 high-
level and low-level consumer APIs. You can use this client by adding a
dependency on the client jar using the following example maven co-ordinates
(you can change the version numbers with new releases):

    
    
    	<dependency>
    	    <groupId>org.apache.kafka</groupId>
    	    <artifactId>kafka-clients</artifactId>
    	    <version>0.9.0.0</version>
    	</dependency>
    

Examples showing how to use the consumer are given in the [javadocs](http://ka
fka.apache.org/090/javadoc/index.html?org/apache/kafka/clients/consumer/KafkaC
onsumer.html).
