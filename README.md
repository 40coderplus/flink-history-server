# Goal

This project is an extension of [Flink History Server](https://nightlies.apache.org/flink/flink-docs-release-1.16/docs/deployment/advanced/historyserver/).

- history-server-spring-boot: a Spring Boot starter. Fetch Flink Job History from different FileSystem(s) and paths. After fetch job archived json, store then to db using JPA.
- history-server-embedded-dashboard: embedded `Flink History Server` web dashboard, provide REST Endpoint
  - /config
  - /overview
  - /jobs/overview
  - /jobs/{jid}/**

Base on the tow module, we can fetch job history, store then to db, view then in web dashboard.

# Custom

- `Consumer<Job> archivedJobConsumer`: default is `FlinkJobJpaMutator`.
  - EG: using Event-Driven pattern. publish to Spring events , using `ApplicationListener` to do some logic.
- `HistoryServerArchiveFetcher`: custom `Consumer<Job>` `Consumer<HistoryServerJobArchive>`.
  - default `Consumer<HistoryServerJobArchive>` is delete after file fetched.
- `FlinkRestApiService`: default is `FlinkRestApiJpaService`.

# Scheduling

Start a scheduled task to fetch archived jobs.

```java
@Scheduled(fixedDelay = 10000, initialDelay = 5000)
public void fetchInterval() {
    logger.info("Schedule check history archive paths at {}", LocalDateTime.now());
    historyServerArchiveFetcher.fetchArchives(refreshDirs);
}
```