# Spark Optimization

## Executors, Cores and Memory

    - How to decide the no. of executors to have, the cores the executors should have and the memory associated with them?
        - A node will have C Cores and M Memory.
        - When we do 'SPARK-SUBMIT', we have to specify
          - No. of executors. (number-executors)
          - No. of cores. (executors-cores)
          - Amount of memory we want to allocate to those cores (executor-memory)
        - We should leave out 1 Core and 1 GB of RAM out of total available Cores & RAM for operating system.
        - How to size an optimal executors?
          - Leave out 1 Core and 1GB of RAM for Hadoop YARN and Operating System.(Node Level)
          - Application Manager: An Application Manager is the one which asks for resources from Resource Manager. For Application Manager to function properly we leave out 1 Executor or (1 Core and 1 GB RAM)(Cluster Level).
          - HDFS Throughput : The HDFS throughput, which is the rate at which you are writing data to HDFS or reading data from HDFS, it deteriorates if we have more than 5 cores per executors as the Garbage Collection mechanism is done more frequently. So it is recommended to have between 3-5 cores per executors.
          - Memory Overhead: It is used for internal system processes. The 'executor-memory' should exclude certain amount of memory for this Memory Overhead. Ideally around MAX of 384 MB or 10 % of executor-memory'
          - How many Executors to have?
            - Calculate how many cores you have after considering above points.
            - Decide how many cores you want per executors. Remember cores per executors should be between 3-5. So let say 5 cores per executors.
            - Then divide the (no. of total cores)/(no. of cores per executors) and you have the total no.of executors.
          - How to allocate memory to these executors?
            - Find out the total memory available and divide it by the total executors.

## Partition and Partition Size

    - 1 core is equal to 1 partition, the no. of memory associated with 1 core as long as it is greater than partition size, processing should occur seamlessly
  
## Shuffle Partitions

### How to set 'spark.sql.shuffle.partition' ?

    - The default size of 'spark.sql.shuffle.partition' is 200.
    - Optimal shuffle partition size should fall between 1-200 MB.
    - To find the optimal count of shuffle partition, divide the dataSize/200MB.
    - To find the data per shuffle partition, divide dataSize/no.of shuffle partition.
    - If the dataPerShufflePartition comes below 1MB or greater than 200 MB we need to handle it in following way
        i. When the dataPerShufflePartition is less than 1 MB
            Take a ideal value of dataPerShufflePartition, divide the totalDataSize/idealDataPerShufflePartition
            For example let's say your dataPerShufflePartition comes to 2.5 kb, you can take the ideal dataPerShufflePartition to be what 10MB, then divide totalDataSize/10MB and you will get the total No. of Shuffle partition.

        ii. When the dataPerPartition is more than 200 MB
            In this case you need to divide the dataSize/200MB to get the ideal no. of shuffle partition.
  
### Partitioning in Apache Spark

    - To set the maximum size of partition : 'spark.sql.files.maxPartitionBytes'

## Bucketing

    - How to decide the optimal no. of buckets
      - Size of dataset = x
      - Optimal bucket size 128 - 200 MB
      - No. of buckets = size of datasets / optimal bucket size.
