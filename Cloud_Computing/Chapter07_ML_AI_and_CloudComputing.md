

7.1 AI Infrastructure on Clouds
7.2 AI for managing Clouds
7.3 AI service s on e xample Amazon SageMaker

# 1 AI Infrastructure on Clouds

Cognitive Cloud Continuum
![](image/Pasted%20image%2020250113162305.png)


Increasing Complexity
- Serving: displaying on user-side
![](image/Pasted%20image%2020250113162321.png)

AI Inference and Serving Technology
- TTFT: time to first token
- TTOT: time to output token
![](image/Pasted%20image%2020250113162520.png)


Reference Architecture
- Inference Execution Engine: VLLM
![](image/Pasted%20image%2020250113162538.png)


Cluster model
- Reference model not published yet
- Typical characteristics
    - `#CPUs` = 30 x `#GPUs `
    - High performance, non blocking connectivity
- Performance (~HPC) vs Scalability (~Cloud)
- Multi layer design: Inference and training layer
![](image/Pasted%20image%2020250113162906.png)



# 2 AI for Cloud Management


Computing power is a key for AI development Developing and deploying AI systems is relatively fast, but maintaining AI systems over time is difficult and expensive. becuase the new data will influence the AI Model 
How to keep the AI infrastructures, AI models, and applications cost efficiently up and running?

- AI widely used in operation of cloud infrastructures to analyse meta information and make fast decisions for
    - Scalability: scale out / scale down
    - Fault tolerance: migrating containers / VMs to prevent anomalies turning into failures
    - Security: identifying on going attacks / breaches, issuing counter measures, mostly as part of intrusion detection systems
    - Automating workflows and management
    - Assisting users with support queries

AI Serviceability: response quality
- New dimension in serviceability of IT infrastructures: response quality
    - Changes over time due new data
    - Changes over time with same data due to cultural shift in the outside world
- Evaluation and verification are continuous tasks
- ![](image/Pasted%20image%2020250113164455.png)


## 2.1 AI in Monitoring and Observability

> Monitoring tells whether a systems is working; observability asks why it isn't working.

- Monitoring: Collecting data and notifying admins about detected issues based on pre defined thresholds / rules Looking for known or predictable failures
- Observability: Understanding what is happening inside a system by looking at the output data Analyse data to assess system's health and fix problems
- Observability established → following actions typically implemented
    - Developing actionable alerts
    - Creating useful dashboards
    - Deploying AI enabled solutions


![](image/Pasted%20image%2020250113164835.png)


Data is the Key
![](image/Pasted%20image%2020250113174207.png)

Data types for observability
- Internal data
    - Metric data: numerical values about the health of the system and its components, for example CPU, memory or network utilization, CPU temperature
    - Logs: textual notes from developers for future users and admins on how to repair or understand the running system (while comments are suitable for the source code)
    - Traces: Automatically generated information characterizes the execution path of specific process, i.e. which method called which other methods with which parameters. Tracing includes unique IDs, operation names, timestamps, logs, events, and indexes
- External data
    - Tickets: Mails and phone calls by user to customer support
    - Tweets and all other types of user feedback on social media



## 2.2 Anomaly 

- Observable deviations from normal behavior ( beyond crashes or overload situations)
- Examples
    - Reduced service quality, degraded states  (latenz)
    - Resource Leaks   : momery leaks
    - Software Aging
    - Endless loops
    - Bugs
- Often precede crashes or other faults
- Detection and remediation 补救；矫正；补习 : manually or with fixed rules & alarms


Definitions
- Symptoms: Cause out of norm behavior of system parameters as a side effect
- Latent fault: Periods of degraded performance that precedes failures. At least 20% of machine failures are preceded by degraded states
- Pre failure state: Performance anomalies often manifest in system level metrics

Failures and degraded state anomalies
- Corrupted
    - Stored state of the service are lost, corrupted and cannot be regenerated.
- Unreachable
    - Service does not respond to requests.
- Degraded
    - Service is available but operates not to 100%
- Masked
    - Faults occur in service components but are completely hidden to the entity using the service


Occurrence of Anomalies
- Statements from research studies (to visualize the high grade of uncertainty and lack of (published) measurements)
    - 34% of incidents due to software, 51% due to misconfiguration
    - Administrator errors responsible for 42% of system failures
    - Service disruption 扰乱，中断 causes: 29% configuration, 34% software, 12% human
    - Google data center (6 weeks field data 10% Hardware faults, 60% Software errors faults, 20% Operation human errors / networking
    - Webservices: 10 20% Hardware faults, 60% Software errors faults, 20% Operation and maintenance faults


State of the art: (Manual) Handling of Anomalies
- Anomalies include unforeseen situations 未预见到的，无法预料的，意料之外的
    - Service specific fault handling covers only known fault scenarios
    - Proactive measures can only cover known situations
- Manual handling of anomalies
    - Slow reaction time due to “human in the loop” 
    - Prone to human error
    - Does not scale for large systems
- Monitoring & automation systems
    - System layers and components monitored individually
    - Fixed rules & thresholds (expert knowledge & continuous maintenance)


Challenges & Requirements for handling anomalies
- Cloud runs user workload: Controlled overhead
- Cloud runs arbitrary workload: Black box VMs
- Cloud is highly dynamic: Adaptive decision making and resource management
- Anomalies precede faults: Fast remediation

![](image/Pasted%20image%2020250113215801.png)


## 2.3 Algorithmic Challenges

false positive: 被误判为 failure, 但不是 failure 

- Potential for false positives
    - Varying workload, dynamic resource utilization
    - System changes (such as upgrades, rollbacks)
    - Irregular and rare component behavior (e.g. big database queries)
    - Seasonal load variations
    - Special events (e.g. sales)
- No training data
    - Experiments with anomalies not feasible in productive systems
    - Cold start: no prior knowledge when deploying new system or component
- Streaming, unbatched operation

![](image/Pasted%20image%2020250113220105.png)


Straightforward AD on Metric Data
- Anomaly can appear anywhere and with any intensity
- Real time detection of degraded state faults

![](image/Pasted%20image%2020250113220147.png)


![](image/Pasted%20image%2020250113220214.png)


## 2.4 Autoscaling

- Autoscaling: adding or removing resources depending on the requirements
- Reactive methods: resource (de --) allocation only when necessary
    - Thresholding: user defined rules initiate scaling when specific conditions occur, e.g. CPU usage exceeds specific threshold
    - Queuing: activity triggered based on metrics such as response time for requests, queue length, and service rate
- Proactive methods: learn typical behavior and use current monitoring data to forecast the workload in near future

![](image/Pasted%20image%2020250113220406.png)

Proactive Auto Scaling Techniques
- Broad range of machine learning techniques already applied
- Most frequent: Reinforcement Learning and Time Series Analysis


![](image/Pasted%20image%2020250113220457.png)


Example: Kubernetes Horizontal Pod Autoscaler
![](image/Pasted%20image%2020250113220515.png)


## 2.5 Kubernetes Horizontal Pod Autoscaler

- Automatically scales number of pods in a replication controller
- Based on
    - CPU utilization or custom metrics
    - Target value for the metric
- Autoscaler checks metrics every 30 seconds
- Sets the number of replications to optimize the metric towards the target value
    - By creating more pods or
    - By reducing the number of pods

- Number of replicas scaled by quota of average current metric value / target value
- No scaling if this quota is within a 0.1 tolerance
    - desiredReplicas = ⌈currentReplicas * currentMetricValue desiredMetricValue ) → This assumes linear scaling!
- Autoscaler scales to highest number of desired replicas in sliding window of 5 min
    - Quick responses to more load, but reduces thrashing#



# 3 AI Services@Cloud platforms

- Services provided by AWS for ML and AI
    - SageMaker : fully managed ML service to build, train, and deploy ML models
    - Rekognition : image and video analysis
    - Polly: turns text into lifelike speech
    - Lex: conversational interfaces using voice and text
    - Comprehend: extract insights from text
    - Translate: for natural language documents
    - Personalize: for recommendations
    - Forecast: for time series data
    - Textract : E xtracting text and data from documents

- Services by Microsoft Azure for AI and ML
    - Databricks: Apache Spark based analytics platform
    - Machine Learning: build, deploy, and share predictive models
    - Bot Service: build, deploy, and manage chatbots.
    - Cognitive Services: set of APIs to add AI features to your applications.
- Services provided by GCP for AI and ML
    - AutoML : pre trained models for image/text classification, object detection
    - Datalab : collaborative environment with tools for building ML models
    - ML Engine: ML training/deploying with hyperparameter tuning, versioning, autoscaling
    - Natural Language: entity recognition, sentiment analysis, syntax analysis
    - Speech to Text, Text to Speech, Translation
    - Vision: analyzing images with object detection, image classification, OCR
    - Video Intelligence: object detection, shot detection, event detection in videos


## 3.1 Example: Amazon SageMaker

- Domain specific service for machine learning tasks, started in late 2017
- Facilitates the steps of typical machine learning workflow
    - Labelling data
    - Building
    - Training
    - Deployment


---

Labelling Data
- Data labelling by humans
- Mechanical workers via amazon or self recruited
- Common labelling types have a predefined task type
- Active learning for faster labelling

![](image/Pasted%20image%2020250113163504.png)



Labelling Data Process
- Data is uploaded to s3
- Labelling Job is submitted, consisting of
    - Instructions to human workers
    - Reference to unlabeled data
    - Definition of Labels, if fixed
    - Email addresses of workers, if self recruited
- Human workers label data via web interface
- Labelling can be accelerated via active learning Active learning used to automatically select which data need to be labelled manually



![](image/Pasted%20image%2020250113163712.png)

---

Building and Training Jobs

- Options to build models
    - Often Jupyter Notebooks
    - Simpler problems via CLI, JSON, REST
- There are many built in algorithms for tasks like image classification, text classification, and clustering
    - ![](image/Pasted%20image%2020250113163750.png)
- SageMaker integrates ML libraries like MXNet , Tensorflow , PyTorch
- Alternatively, customers can build docker containers to
    - Use specific unsupported versions
    - Use a ML framework that’s not provided by SageMaker
    - Use unsupported additions to a framework


----

Amazon SageMaker : Deployment

- Trained models can be deployed as
    - Web endpoints
    - Batch transform
- Autoscaling deployed models based on
    - Target metric
    - Scaling policy
    - Integrated with CloudWatch


![](image/Pasted%20image%2020250113163959.png)