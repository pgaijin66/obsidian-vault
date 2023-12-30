---
id: 86b8042b-cd79-45ea-ad8b-3131273024ab
---

# What is High Cardinality | Last9
#Omnivore

[Read on Omnivore](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef)
[Read Original](https://last9.io/blog/what-is-high-cardinality)

## Highlights

> high cardinality plays a crucial role in understanding the complexity and depth of time series data. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#b16fe76a-5bb8-4261-8b08-3c0e3fe57190) 

> high cardinality refers to a metric or attribute with a large number of distinct values or unique entities. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#4090d5a6-3ecf-405c-8dbd-5986ea5a9a7b) 

> It signifies the richness, granularity, and diversity encapsulated within a dataset. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#6d4d4be0-47f4-4a00-87a1-5ce8b03760a3) 

> own set of challenges and considerations when it comes to storage efficiency, query performance, visualization, and overall data analysis. In this post, we delve into the depths of what is high cardinality, exploring its implications and limitations. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#09cd530f-b39d-4b65-bb27-50b2f99efda6) 

> In the context of observability, cardinality refers to the number of distinct values or unique entities that a specific attribute or field can have within a system or dataset. It is a measure of the diversity or variability of the data in that particular attribute. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#cfffb0bc-40d4-416b-9980-7932409d1d98) 

> ## High Cardinality and Time Series data
> 
> In the context of time series data, high cardinality refers to a time series metric or attribute that exhibits a large number of distinct values or unique entities over time. It indicates a significant level of diversity or variability in the values of that specific metric across different time points. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#7fabc998-230a-4891-92e7-2992c76e2499) 

> high cardinality in time series data can also present challenges in terms of storage, processing, and analysis. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#be1a4ff6-fdfe-4f84-a208-4b9b48a9f782) 

> Moreover, visualizing and interpreting high cardinality time series data can be complex, as displaying every unique value may result in cluttered or unreadable plots.
> 
> To address these challenges, it's common to aggregate or summarize high cardinality time series data into meaningful intervals or groups. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#a503bb72-7f33-448e-ab42-aa0e95aea73c) 

> Aggregation techniques such as averaging, min-max calculations, or histogram binning can help reduce the number of distinct values while preserving important patterns or trends.
> 
> ## What is High Cardinality Metrics? [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#b9f4d87f-d0f4-4051-b6bc-b3290498b69f) 

> A high cardinality metric refers to a metric or attribute that exhibits a large number of distinct values or unique entities within a dataset or system. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#a09f8aa0-cfd8-41fc-ab24-9dcbfb8160b3) 

> It represents a high level of diversity or variability in the values of that particular metric. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#f7bc8a2d-5e51-45d5-866e-29e79728fca0) 

> For example, in a web application, a high cardinality metric could be the user agent string, which represents the web browser and operating system used by each visitor. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#6bd5d25b-e5da-47e3-8981-334382c411e7) 

> High cardinality metrics often come into play when dealing with user-related data, such as the user ID. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#a6813dfe-b9e2-4a68-9d3a-5adb9e157af9) 

> high cardinality can arise in various contexts, particularly when dealing with metrics and labels associated with the cluster's resources [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#0410c66f-cf65-4817-9200-20610529a458) 

> Labels and selectors: [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#ac20d1ab-25f3-468c-8097-eb45b6477ed9) 

> If there are a large number of distinct label values or a significant number of labels associated with resources, it can result in high cardinality. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#a7b926ce-bea5-4cec-86f2-45668a29907a) 

> Pod or container names: [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#96971aa8-d811-4e91-bbd7-fe81d5c67390) 

> In large-scale deployments with numerous pods or containers, the sheer number of distinct names can lead to high cardinality. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#30104b85-1330-4f2b-9b72-d6294043a458) 

> This can impact various operations, such as log aggregation, monitoring, or debugging, where filtering or analysis based on individual pod or container names is required. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#8b66bb4f-6528-4149-b52a-6c3b84162417) 

> Metrics and monitoring: [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#1659200c-8283-43a4-83c1-112d80d01771) 

> When these metrics are tagged with labels, the unique combination of a large number of metrics and distinct label values can result in high cardinality. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#c03cc6ef-76a2-492a-b1ad-69bb07df30af) 

> Namespaces: [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#28db292f-5127-4bc2-9c0f-a35372caa46e) 

> If there are many distinct namespaces within a cluster, it can contribute to high cardinality. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#425224e8-8cae-4d10-abb9-d5f4741b51c6) 

> Custom labels and annotations: [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#62f7fbb7-f0a6-4aad-a7a7-443768a3292a) 

> an excessive number of custom labels or annotations, or a large number of unique values for these custom attributes, can contribute to high cardinality. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#67306e02-6c99-4e1f-acc6-6178fa86a07d) 

> Storage requirements: High cardinality metrics can result in a significant increase in storage requirements. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#14ecf48d-a1e2-4c48-a451-c308f8feb444) 

> Performance impact: High cardinality metrics can impact system performance, especially during data ingestion, querying, and analysis. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#3b89db9d-ff14-4704-9b3e-7ca791bce201) 

> Scalability issues: As the cardinality of metrics increases, the scalability of data processing systems may be affected. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#1ed191d4-3490-4674-b011-04ca9c69f08b) 

> Query efficiency: When querying high cardinality metrics, it becomes more challenging to retrieve and analyze data efficiently. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#bdb1c834-9eb9-4cc1-8a3e-f47f2b137136) 

> Visualization and analysis complexity: Visualizing high cardinality data can be challenging, especially in traditional charting tools. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#8561757a-718a-416b-949f-88cdc917ee0d) 

> Indexing and metadata management: Managing indexes and metadata for high cardinality metrics can be resource-intensive. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#715b9081-c0c0-4472-ba1e-2b0dd35a6f1f) 

> it's important to carefully evaluate the necessity of high cardinality metrics, consider appropriate data modeling and aggregation strategies, and optimize the observability platform’s resources and configurations accordingly. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#58370305-694c-4da9-ae49-d75f36d8df2a) 

> It may also be beneficial to explore specialized databases or techniques designed to handle high cardinality data, such as time series databases like [Levitate](https://last9.io/levitate-tsdb) or distributed systems with built-in cardinality management capabilities. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#fd66943b-cce1-4cc6-9054-e25fb5773a34) 

> ## Prometheus and High Cardinality [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#d0824a2c-5dde-4d07-a975-a81361c8adfb) 

> High cardinality in Prometheus refers to time series metrics that have a large number of distinct labels or label value combinations. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#668473c1-53d9-4d12-ac34-aebf05d2dfce) 

> High cardinality metrics can lead to increased storage requirements, longer query evaluation times, and increased memory usage. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#3a3374a6-1fd9-437c-83ef-79eb629bd697) 

> As the number of distinct label values grows, the Prometheus server needs to maintain indexes and metadata for efficient querying, which can impact scalability and resource utilization.
> 
> To handle high cardinality in Prometheus effectively [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#ad6f8c1e-40a6-4adf-ba25-161f48046ad3) 

> consider the following best practices: [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#b0a39850-5612-4f01-b025-0db6d80fb53d) 

> Select labels carefully: Use labels judiciously and avoid creating labels with a large number of distinct values unless necessary. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#f95a0bf4-c3d3-4fc7-8094-0ef9d27a4173) 

> Evaluate the cardinality implications before adding new labels to time series. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#e40dc02c-b4cf-46e0-b73d-f45c33108cbc) 

> Use relabeling and aggregation: [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#552a1b90-1dab-44af-a3f6-8aa2f9fe2718) 

> Prometheus provides relabeling and aggregation mechanisms to reduce the cardinality of metrics. By applying appropriate relabeling rules and aggregating data at collection time, you can reduce the number of distinct label value combinations. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#9db823fa-e890-433c-8d96-6018ba31ccbe) 

> Enable chunking and compression: [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#05157412-f9c4-42b7-b244-587f14622064) 

> Configure Prometheus to use chunking and compression mechanisms to optimize storage efficiency. This [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#5bffad17-528b-4f66-a8b4-1f456070e849) 

> Use metric naming conventions: Employ a consistent and meaningful naming convention for metric names. Well-defined naming conventions make it easier to manage and analyze high cardinality metrics. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#7ab644d3-fa64-4977-88cc-efb4a1f2875c) 

> Leverage downsampling: [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#bcadf81f-f4fd-41fb-bfad-59c3d91f969e) 

> If the high cardinality metric does not require high-resolution data, consider downsampling the data to reduce storage and processing overhead. Downsampled metrics can be stored in a separate TSDB or aggregated over longer time intervals. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#3ced9d1f-e4ad-4e82-82c8-6242bec17dab) 

> Monitor and optimize: Continuously monitor the resource utilization of Prometheus, including memory usage, disk space, and query latencies. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#ef7d53fc-31c5-4ca8-a8c8-77cbe2bfb103) 

> Optimize the Prometheus config based on the observed performance characteristics and cardinality patterns of your specific use case. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#4f704e94-8b7e-4734-8b53-95d7c6567ce9) 

> Multiplicative cardinality can demonstrate the exponential growth of cardinality when dealing with a large number of dimensions or attributes. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#d3c110b4-e0c0-4665-b07e-594088bd9406) 

> ## High Cardinality Metrics in Grafana
> 
> High cardinality metrics can present challenges when used with Grafana. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#3a293e18-5483-4024-b010-6d1ba646be7d) 

> Query performance: [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#7a09adf3-5958-433c-95e4-64d592def726) 

> Visualization complexity: [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#43fcf223-6b46-4b9f-a532-6fa5268a1246) 

> Resource utilization: [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#1899ad1d-37ba-486a-960a-e01465610313) 

> Dashboard design considerations: [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#18553180-f1ac-4710-aba1-ab9d4015e59a) 

> Alerting and anomaly detection: [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#e7eaca18-9ff7-4dac-a598-bbc43f798e42) 

> Retention Strategies for High Cardinality Metrics [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#2586fee9-e704-48ac-a9d8-481914cedef5) 

> Retention periods are typically defined based on business requirements, regulatory compliance, and storage considerations. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#be9e3993-480d-4aee-b73d-416f0159b52b) 

> High cardinality metrics can influence retention decisions. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#8fd23d81-2210-4023-b5ab-c66ff5bcf912) 

> Storing high cardinality metrics for a longer retention period can consume significant storage resources due to the large number of distinct values. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#d5d87b24-9678-45e3-b367-087aaede29af) 

> high cardinality is a critical concept in observability [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#9e370736-7ce8-4818-915d-bfc8fa779aa5) 

> It refers to metrics or attributes with a large number of distinct values or unique entities. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#532fb2a7-4f4d-42fe-819c-9e5c8135e7f9) 

> High cardinality provides richness, granularity, and diversity in datasets, enabling detailed analysis and uncovering patterns and anomalies. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#b47435ff-33cd-4c42-9e9e-fbc7cfd173fa) 

> However, high cardinality also presents challenges in terms of storage efficiency, query performance, and visualization. [⤴️](https://omnivore.app/me/what-is-high-cardinality-last-9-188bc0e29ef#d01e63e8-7ffb-41e4-ae9f-fe61b52bff6a) 

