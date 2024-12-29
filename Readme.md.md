
# Spark Streaming Documentation

This documentation provides an overview of Spark Streaming concepts, including definitions and examples.

## 1. Spark Streaming
- **Definition**: Spark Streaming is an extension of the core Spark API that enables scalable, high-throughput, and fault-tolerant stream processing of live data streams.

- **Example**:
  ```python
  from pyspark.streaming import StreamingContext
  
  # Create a local StreamingContext with batch interval of 1 second
  ssc = StreamingContext(sc, 1)
  
  # Create a DStream that monitors a directory for new text files
  lines = ssc.textFileStream("path/to/directory")
  
  # Split each line into words
  words = lines.flatMap(lambda line: line.split(" "))
  
  # Print the first 10 words
  words.pprint()
  
  # Start the streaming context and await termination
  ssc.start()
  ssc.awaitTermination()
  ```

## 2. DStreams (Discretized Streams)
- **Definition**: DStreams represent a continuous stream of data, divided into batches, and is the basic abstraction provided by Spark Streaming.

- **Example**:
  ```python
  # Create a DStream from a TCP source (port 9999)
  lines = ssc.socketTextStream("localhost", 9999)
  
  # Process each RDD in the DStream
  word_counts = lines.flatMap(lambda line: line.split(" ")).map(lambda word: (word, 1)).reduceByKey(lambda x, y: x + y)
  ```

## 3. Stateful Transformations
- **Definition**: Stateful transformations allow Spark Streaming to keep track of data across batches. The most common stateful transformation is `updateStateByKey`.

- **Example**:
  ```python
  # Define a function to update state
  def updateFunction(new_values, last_sum):
      return sum(new_values) + (last_sum or 0)
  
  # Apply updateStateByKey to maintain a running word count
  running_counts = word_counts.updateStateByKey(updateFunction)
  ```

## 4. Window Operations
- **Definition**: Window operations allow transformations over a sliding window of data in Spark Streaming.

- **Example**:
  ```python
  # Windowed word count, with a window duration of 10 seconds and sliding interval of 5 seconds
  windowed_word_counts = word_counts.reduceByKeyAndWindow(lambda x, y: x + y, 10, 5)
  ```

## 5. Checkpointing
- **Definition**: Checkpointing in Spark Streaming allows the system to recover from failures. It saves the state of DStreams and makes the system fault-tolerant.

- **Example**:
  ```python
  # Enable checkpointing
  ssc.checkpoint("hdfs://checkpoint-directory")
  
  # Use checkpointing to recover from stateful transformations
  running_counts.checkpoint(10)
  ```

## 6. Input Sources
- **Definition**: Spark Streaming can receive data from various sources, including Kafka, HDFS, and TCP sockets.

- **Example**:
  ```python
  # Example of Kafka input stream
  kafka_stream = KafkaUtils.createStream(ssc, "zookeeper:2181", "consumer-group", {"topic": 1})
  
  # Process Kafka data
  lines = kafka_stream.map(lambda message: message[1])
  ```

## 7. Output Operations
- **Definition**: Output operations are performed to push the processed data to external systems like databases, files, or dashboards.

- **Example**:
  ```python
  # Print first 10 elements of each batch
  word_counts.pprint()

  # Save word counts to a text file
  word_counts.saveAsTextFiles("output-directory")
  ```

## 8. Fault Tolerance
- **Definition**: Spark Streaming is fault-tolerant and can recover lost data due to failures using RDD lineage and checkpointing.

- **Example**:
  ```python
  # Enable checkpointing to make the system fault-tolerant
  ssc.checkpoint("/path/to/checkpoint/dir")
  ```

## 9. Batch Interval
- **Definition**: The batch interval in Spark Streaming determines how often the data will be processed in micro-batches.

- **Example**:
  ```python
  # Set batch interval to 2 seconds
  ssc = StreamingContext(sc, 2)
  ```

## 10. Performance Tuning
- **Definition**: Performance tuning in Spark Streaming involves adjusting parameters like batch size, memory allocation, and parallelism for efficient stream processing.

- **Example**:
  ```python
  # Increase the number of partitions for better parallelism
  lines = lines.repartition(10)
  
  # Tuning the batch interval for performance
  ssc = StreamingContext(sc, 5)
  ```

