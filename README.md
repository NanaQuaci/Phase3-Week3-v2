# Performance Testing with JMeter - Swag Labs

![Build Status](https://img.shields.io/jenkins/build?job=magento_performance&server=http://localhost:8080)
![GitHub last commit](https://img.shields.io/github/last-commit/NanaQuaci/Phase3-Week3-v2)
![GitHub repo size](https://img.shields.io/github/repo-size/NanaQuaci/Phase3-Week3-v2)

## Overview

This project evaluates the **performance, scalability, and reliability** of the [Swag Labs](https://www.saucedemo.com/) web application using **Apache JMeter 5.6.3**. The tests simulate real user behavior to measure:

* Response times
* Throughput
* Error rates
* System scalability

---

## Application Under Test

Critical workflows tested:

* User Login
* Product Search
* Viewing Product Details
* Adding Products to Cart
* Checkout Process

---

## Performance Goals

| Metric        | Target                      |
| ------------- | --------------------------- |
| Response Time | < 2 seconds                 |
| Throughput    | 500 requests/sec            |
| Error Rate    | ≤ 1%                        |
| Scalability   | Test under increasing loads |

---

## Project Structure

```
Phase3-Week3/
├─ FakestoreAPI Performance Test.jmx    # JMeter Test Plan
├─ results.jtl                        # Test results
├─ reports/                           # HTML Dashboard
├─ Jenkinsfile                        # CI/CD pipeline
└─ README.md                          # Documentation
```

---

## Prerequisites

* Java 21
* Apache JMeter 5.6.3
* Docker (optional, for containerized Jenkins)
* Jenkins (optional, for CI/CD pipeline)

---

## Running Tests Locally

Run JMeter in non-GUI mode:

```bash
jmeter -n -t Magento_Performance_Testing.jmx -l results.jtl -e -o reports
```

* `results.jtl` → raw test data
* `reports/` → HTML dashboard for visualization

---

## CI/CD Integration (Jenkins)

Pipeline automates:

* Checkout from GitHub
* Running JMeter tests
* Publishing HTML reports

Example `Jenkinsfile` snippet:

```groovy
pipeline {
    agent any
    stages {
        stage('Checkout') { steps { git branch: 'main', url: 'https://github.com/NanaQuaci/Phase3-Week3.git' } }
        stage('Run JMeter Test') { steps { sh 'jmeter -n -t Magento_Performance_Testing.jmx -l results.jtl -e -o reports' } }
        stage('Publish Report') { steps { publishHTML(target: [reportDir: 'reports', reportFiles: 'index.html', reportName: 'JMeter HTML Report']) } }
    }
}
```

---

## Test Reports

HTML Dashboard reports generated for:

* Baseline
* Load
* Stress
* Endurance

Reports include:

* Response time percentiles
* Throughput over time
* Error rates
* Graphs for visualization

---

## Author

* **Nana Quaci**

---

## License

Educational use / Personal project
