# Peleton

`The Self Driving Database`

## Introduction

- Challenges
  - understand an application's workload, OLTP or OLAP.
  - optimization decisions rely on the above.
  - needs to forecast resource utilization trends

 - Areas to Apply
  - Physical
    - Indexes
    - Materialized Views
    - Storage Layout
  - Data
    - Location
    - Partitioning
  - Runtime
    - Resources
    - Configuration Tuning
    - Query Optimizations

- For each optimization action, DBMS needs to estimate effect on DB.
- Optimizations need to be applied in such a way that there is no perceptible impact to the application during deployment.
- Latency is the most important metric in a DBMS as it captures all aspects of performance.

- Components
  - Workload classification
    - clustering the workload
      - use runtime metrics
      - query logical semantics
  - Workload Forecasting
    - Auto-regressive-moving average model
  - Action Planning and Execution
    - Advantage of having tight coupling of the autonomous components and DBMS architecture is most evident, as they can provide feedback to each other.
    - Action generation
    - Action planning
      - Receding horizon-control model.
    - Deployment

- Additional Considerations
  - Explanation of chosen action plans.
  - Priotity across components

- See Noise Page    
  
