## Title: NM crash with NPE when LogAggregationService#stopContainer called for absent container

## Description
NM crash during of log aggregation. Ran Pi job with 500 container and killed application in between

## Stack Trace:
```
java.lang.NullPointerException,  
    at org.apache.hadoop.yarn.server.nodemanager.containermanager.logaggregation.LogAggregationService.stopContainer(LogAggregationService.java:422),  
    at org.apache.hadoop.yarn.server.nodemanager.containermanager.logaggregation.LogAggregationService.handle(LogAggregationService.java:456),  
    at org.apache.hadoop.yarn.server.nodemanager.containermanager.logaggregation.LogAggregationService.handle(LogAggregationService.java:68)
```

### Analysis:
Looks like for absent container also stopContainer is called
```java
 case CONTAINER_FINISHED:
    LogHandlerContainerFinishedEvent containerFinishEvent = (LogHandlerContainerFinishedEvent) event;
    stopContainer(containerFinishEvent.getContainerId(), containerFinishEvent.getExitCode());
    break;
```

Event EventType: KILL_CONTAINER sent to absent container container_e51_1442063466801_0001_01_000101

Should skip when `null == context.getContainers().get(containerId)`