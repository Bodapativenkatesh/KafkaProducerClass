# KafkaProducerClass
It is used for send messages to kafka
import java.io.IOException;
import java.nio.charset.Charset;
import java.util.List;
import java.util.Properties;

import org.apache.commons.io.IOUtils;

import kafka.javaapi.producer.Producer;
import kafka.producer.KeyedMessage;
import kafka.producer.ProducerConfig;

public class KakfaProducer {
	 
    public static void kafka(String... args) throws IOException, InterruptedException {
        Properties props = new Properties();
        props.put("metadata.broker.list", "localhost:9092");
		props.put("serializer.class", "kafka.serializer.StringEncoder");
		props.put("partitioner.class","kafka.producer.DefaultPartitioner");
		props.put("request.required.acks", "1"); 
        // Topic Name
        String topic = "Hive_Topic";        
        // Create Kafka Producer
        ProducerConfig config = new ProducerConfig(props);
        Producer<String, String> producer = new Producer<>(config);     
        // Read Data from File Line by Line
        List<String> stockPrices = IOUtils.readLines(
                KakfaProducer.class.getResourceAsStream("output.txt"),
                Charset.forName("UTF-8")
        );
        // Send Each Line to Kafka Producer and Sleep
        for(String line : stockPrices){
            System.out.println(line);
            if(line.startsWith("Date")) continue;
            producer.send(new KeyedMessage<String, String>(topic, line));
            Thread.sleep(10L);
        }
        producer.close();
    }
}
