## Producer API

We encourage all new development to use the new Java producer. This client is
production tested and generally both faster and more fully featured than the
previous Scala client. You can use this client by adding a dependency on the
client jar using the following example maven co-ordinates (you can change the
version numbers with new releases):

    
    
    	<dependency>
    	    <groupId>org.apache.kafka</groupId>
    	    <artifactId>kafka-clients</artifactId>
    	    <version>0.9.0.0</version>
    	</dependency>
    

Examples showing how to use the producer are given in the [javadocs](http://ka
fka.apache.org/090/javadoc/index.html?org/apache/kafka/clients/producer/KafkaP
roducer.html).

For those interested in the legacy Scala producer api, information can be
found [ here](http://kafka.apache.org/081/documentation.html#producerapi).