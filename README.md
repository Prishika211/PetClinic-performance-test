# Performance Testing Report – Spring PetClinic 

This repository contains **comprehensive performance testing results and analysis** for the **Spring Boot PetClinic** application, covering **backend capacity, response time, stability**, and **UI performance**.  
The tests were executed using **Apache JMeter, Gatling, and Lighthouse**, supported by **Grafana, VisualVM, JMX, and JFR monitoring**.

---

## 🧪 Applications Under Test

### Spring Boot PetClinic (Backend)
- Version: `4.0.0-SNAPSHOT`
- Java: OpenJDK 17
- Server: Embedded Tomcat
- Database: H2 (In-Memory)
- URL: http://localhost:8080/

### Spring PetClinic UI
- Tool: Lighthouse (User Flow)
- Browser: Chromium (Desktop Mode)

---

## 🛠️ Tools & Technologies

| Category | Tools |
|-------|------|
| Load & Capacity Testing | Apache JMeter, Gatling |
| UI Performance | Lighthouse (User Flow) |
| Monitoring | VisualVM, Grafana, JMX, JFR, PerfMon |
| JVM & Server | G1GC, Tomcat, HikariCP |
| Reporting | HTML Reports, Aggregate Reports, Dashboards |

---

## 📊 Test Coverage Summary

| Test Type | Tool | Objective |
|---------|------|----------|
| Capacity Test | JMeter | Identify backend saturation point |
| Capacity Test | Gatling | Validate max sustainable load |
| Response Time Test | JMeter | Measure latency below saturation |
| Response Time Test | Gatling | SLA validation at 80% capacity |
| Stability Test | JMeter | Long-duration reliability |
| UI Performance Test | Lighthouse | Frontend rendering & interaction |

---

## 🔥 Backend Capacity Test – JMeter

### 🎯 Objective
Determine the **saturation point** of Spring PetClinic backend and identify bottlenecks.

### 🧠 Key Findings
- **Saturation Point:** ~3000 Virtual Users
- **Primary Bottleneck:** Database connection pool (HikariCP – 10 connections)
- CPU and Memory were **not limiting factors**
- Failure caused by **DB connection starvation**

### 🚨 Saturation Indicators
- Response time spike (>30 seconds)
- Throughput plateau (~107 req/sec)
- Error rate spike (~0.7–2%)
- SQL connection timeout exceptions

### 📈 Root Cause
- HikariCP pool fully exhausted (10/10 active)
- Requests queued → timeouts → thread blocking
- CPU < 20%, Heap healthy → confirms DB-layer bottleneck

---

## ⚡ Backend Capacity Test – Gatling

### 🎯 Saturation Point
- **Identified at:** ~2000 Virtual Users

### 🔍 Observations
- Throughput plateaus at 2000 VU
- Response times degrade sharply post-saturation
- Connection refused errors beyond 2000 VU

### 🧠 Root Cause
- Tomcat thread pool exhaustion
- Blocking `SecureRandom` entropy source (Windows-specific)
- Session creation delays blocking request threads

---

## ⏱️ Response Time Test – JMeter

### 🎯 Objective
Validate response time behavior **below saturation**.

### ⚙️ Load Profile
- Virtual Users: **1700**
- Duration: **30 minutes**
- Ramp-up: 660 seconds

### 📊 Key Metrics (Overall)
- Avg Response Time: **5.3 sec**
- 95th Percentile: **~9 sec**
- 99th Percentile: **~11 sec**
- Throughput: **~26.9 req/sec**
- Error Rate: **0.00%**

### ✅ Conclusion
- System remained stable for 30 minutes
- No errors or resource exhaustion
- Ideal load level for SLA benchmarking

---

## ⚡ Response Time Test – Gatling

### 🎯 Load
- **1600 VU (80% of saturation)**

### 📈 Observations
- Stable throughput
- Mostly sub-second response times
- Zero errors
- CPU, memory, threads, and GC well within limits

### ✅ Result
Application meets performance expectations at near-capacity load.

---

## 🧘 Stability Test – JMeter

### 🕒 Test Details
- Duration: **4 hours**
- Load: **100 Virtual Users**
- Total Requests: **2.6M**

### 📊 Results
- Error Rate: **0.00%**
- Avg Response Time: **~527 ms**
- 99th Percentile: **< 2 sec**
- Throughput: **~183 req/sec**

### 🧠 JVM Observations
- Healthy heap “sawtooth” pattern
- No memory leaks
- Stable CPU (30–40%)
- Thread usage far below limits

### ✅ Verdict
**PASSED** – Application is highly stable under sustained load.

---

## 🖥️ UI Performance Test – Lighthouse

### 🎯 Objective
Evaluate frontend rendering, responsiveness, and interaction stability.

### ⚙️ Execution
- Loops: **3**
- Mode: Desktop
- Tool: Lighthouse User Flow

### 📌 User Journeys Covered
- Home Page
- Find Owner
- Owner Details
- Add Pet
- Form Submission

### 📈 UI Metrics Summary
- Fast First Contentful Paint (FCP)
- Stable Largest Contentful Paint (LCP)
- Minimal Total Blocking Time (TBT)
- Immediate Time to Interactive (TTI)
- No layout shifts or rendering delays

### ✅ Conclusion
Frontend is **stable, responsive, and production-ready** for desktop users.

---

## 🛠️ Recommendations

### Backend
- Increase HikariCP pool (10 → 30–50)
- Tune DB max connections
- Enable API-level caching
- Optimize DB queries & indexing
- Fix SecureRandom entropy blocking

### Testing Strategy
- Keep response time tests at 70–80% capacity
- Add soak & spike testing
- Introduce horizontal scaling tests

### UI
- Enable GZIP/Brotli compression
- Add browser caching for static assets
- Integrate Lighthouse into CI/CD
- Test mobile & low-bandwidth profiles

---

## 🏁 Final Conclusion

The Spring Boot PetClinic application demonstrates:

- **Excellent stability**
- **Predictable response times**
- **Clear and identifiable saturation limits**
- **Well-optimized UI performance**

With minor backend tuning—primarily at the **database and entropy source level**—the system is well-positioned for production-scale traffic and future growth.

---

📌 *This repository serves as a complete reference for backend and UI performance validation of Spring PetClinic.*
