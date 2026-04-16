# Enhanced Experiments — Intelligent and Sustainable Cloud-based Resource Management and Scheduling

**MSc Dissertation — Enhanced Phase 1 + Phase 2**
**Author:** Nandakishore
**Framework:** CloudSim 7.0.1 | **Language:** Java | **Build:** Maven

---

## Overview

This repository contains enhanced and extended simulation experiments for my MSc dissertation:

> *Intelligent and Sustainable Cloud-based Resource Management and Scheduling*

This repo serves two purposes:

1. **CloudSimEx2** — fills the missing 2-VM gap in Phase 1, completing the worker node scaling sequence from 1 to 5 VMs.
2. **Phase 2 experiments** — scale the workload alongside the VMs to test whether the system maintains consistent performance as both demand and infrastructure grow together.

---

## Repository Structure

```
trail-v104/
├── CloudSimEx2.java        ← Phase 1 gap filler: 2 VMs, 2 cloudlets
├── CloudSimPhase2Ex1.java  ← Phase 2: 1 VM,  1 cloudlet
├── CloudSimPhase2Ex2.java  ← Phase 2: 2 VMs, 2 cloudlets
└── CloudSimPhase2Ex3.java  ← Phase 2: 3 VMs, 3 cloudlets
```

---

## Part 1 — CloudSimEx2: The Missing 2-VM Experiment

### Why It Was Added

The original Phase 1 experiments (in [phase1](https://github.com/nandakishore-78/phase1)) jump from **1 VM (Ex1) directly to 3 VMs (Ex3)**. This leaves a gap — we never see the exact moment when moving from shared to dedicated VMs first makes a difference. CloudSimEx2 fills that gap.

### What It Does

```
Host [1000 MIPS, 2048 MB RAM]
 ├── VM0 [250 MIPS] ← Cloudlet 0  (dedicated — full 250 MIPS)
 └── VM1 [250 MIPS] ← Cloudlet 1  (dedicated — full 250 MIPS)
```

- 2 VMs, 2 cloudlets — one job per VM, no sharing, no idle VMs
- This is the **optimal configuration**: zero waste, zero contention
- **Expected finish time: 1000 seconds**

### Why This Is Important

CloudSimEx2 is the first point in the experiment sequence where:
- Every VM has a job (no waste)
- Every job has a VM (no contention)

It confirms that **2 dedicated VMs for 2 jobs is the sweet spot**. Ex3, Ex4, and Ex5 all go beyond this optimal point by adding idle VMs.

---

## Part 2 — Phase 2: Scaling Workload With VMs

### The Phase 2 Research Question

> *If we increase both VMs and jobs together — keeping the ratio at 1 job per VM — does execution time stay constant as the system grows?*

Phase 1 held the workload fixed and scaled VMs. Phase 2 does the opposite — both grow together. This tests **horizontal scalability**: can the system handle more work by adding proportionally more resources, without slowing down?

### Phase 2 Experiment Files

#### `CloudSimPhase2Ex1.java` — 1 VM, 1 Cloudlet (Phase 2 Baseline)

```
Host [1000 MIPS]
 └── VM0 [250 MIPS] ← Cloudlet 0
```

- 1 job, 1 VM — job gets full 250 MIPS, no sharing
- **Expected finish time: 1000 seconds**

---

#### `CloudSimPhase2Ex2.java` — 2 VMs, 2 Cloudlets

```
Host [1000 MIPS]
 ├── VM0 [250 MIPS] ← Cloudlet 0
 └── VM1 [250 MIPS] ← Cloudlet 1
```

- Workload doubled, VMs doubled — each job still gets full 250 MIPS
- No idle VMs, no contention
- **Expected finish time: 1000 seconds — same as Phase2Ex1**

---

#### `CloudSimPhase2Ex3.java` — 3 VMs, 3 Cloudlets

```
Host [1000 MIPS]
 ├── VM0 [250 MIPS] ← Cloudlet 0
 ├── VM1 [250 MIPS] ← Cloudlet 1
 └── VM2 [250 MIPS] ← Cloudlet 2
```

- Workload tripled, VMs tripled — each job still gets full 250 MIPS
- No idle VMs, no contention, no wasted resources
- **Expected finish time: 1000 seconds — same as Phase2Ex1 and Phase2Ex2**

---

## Results

### CloudSimEx2 in Context of Phase 1

| Experiment | VMs | Cloudlets | MIPS per Job | Finish Time | Idle VMs |
|---|---|---|---|---|---|
| CloudSimEx1 | 1 | 2 (shared) | 125 MIPS | 2000 sec | 0 |
| **CloudSimEx2** | **2** | **2 (dedicated)** | **250 MIPS** | **1000 sec** | **0** |
| CloudSimEx3 | 3 | 2 (dedicated) | 250 MIPS | 1000 sec | 1 |
| CloudSimEx4 | 4 | 2 (dedicated) | 250 MIPS | 1000 sec | 2 |
| CloudSimEx5 | 5 | 2 (dedicated) | 250 MIPS | 1000 sec | 3 |

CloudSimEx2 is the **exact optimal point** — best performance, zero waste.

---

### Phase 2 Results

| Experiment | VMs | Cloudlets | MIPS per Job | Finish Time | Wasted VMs |
|---|---|---|---|---|---|
| Phase2Ex1 | 1 | 1 | 250 MIPS | 1000 sec | 0 |
| Phase2Ex2 | 2 | 2 | 250 MIPS | 1000 sec | 0 |
| Phase2Ex3 | 3 | 3 | 250 MIPS | 1000 sec | 0 |

```
Finish
 Time
 (sec)
 1000 |  *-----------*-----------*
      |
      +----+-------+-------+-------
           1       2       3     VMs + Cloudlets (scaled together)
```

Finish time stays flat at 1000 seconds as both VMs and cloudlets scale — confirming **efficient horizontal scaling**.

---

## Key Findings

1. **CloudSimEx2 identifies the optimal provisioning point.** 2 VMs for 2 jobs is the first configuration with no contention and no waste simultaneously.

2. **Phase 2 confirms horizontal scaling works.** When VMs and workload scale together proportionally, execution time does not increase — the system handles more work in the same time.

3. **This proves sustainable scaling is achievable.** Growing infrastructure in step with demand maintains performance without over-provisioning. Every VM is used, nothing is idle, nothing is wasted.

4. **Contrast with Phase 1 over-provisioning.** Phase 1 (Ex3–Ex5) added VMs beyond the workload — performance stayed the same but resources were wasted. Phase 2 avoids this entirely by matching supply to demand.

---

## How to Run

### Prerequisites
- Java JDK 21
- Maven 3.x
- CloudSim 7.0.1 (available at [Cloudslab/cloudsim](https://github.com/Cloudslab/cloudsim))

### Steps

```bash
# 1. Clone CloudSim 7.0.1 and build it
git clone https://github.com/Cloudslab/cloudsim.git
cd cloudsim
mvn clean install

# 2. Place these files into:
#    modules/cloudsim-examples/src/main/java/org/cloudbus/cloudsim/examples/

# 3. Run CloudSimEx2 (Phase 1 gap filler)
mvn exec:java -pl modules/cloudsim-examples/ \
  -Dexec.mainClass=org.cloudbus.cloudsim.examples.CloudSimEx2

# 4. Run Phase 2 Experiment 1
mvn exec:java -pl modules/cloudsim-examples/ \
  -Dexec.mainClass=org.cloudbus.cloudsim.examples.CloudSimPhase2Ex1

# 5. Run Phase 2 Experiment 2
mvn exec:java -pl modules/cloudsim-examples/ \
  -Dexec.mainClass=org.cloudbus.cloudsim.examples.CloudSimPhase2Ex2

# 6. Run Phase 2 Experiment 3
mvn exec:java -pl modules/cloudsim-examples/ \
  -Dexec.mainClass=org.cloudbus.cloudsim.examples.CloudSimPhase2Ex3
```

### Expected Output Format

```
========== OUTPUT ==========
Cloudlet ID    STATUS    Data center ID    VM ID    Time    Start Time    Finish Time
    0          SUCCESS        2               0      1000      0.0          1000.0
    1          SUCCESS        2               1      1000      0.0          1000.0
```

---

## Related Repositories

| Repo | Contents |
|---|---|
| [phase1](https://github.com/nandakishore-78/phase1) | Original Phase 1 experiments: CloudSimEx1, Ex3, Ex4, Ex5 |
| [cloudsim-7.0.1-trail](https://github.com/nandakishore-78/cloudsim-7.0.1-trail) | Full CloudSim project with all experiments and complete documentation |

---

## Dissertation Context

**Title:** Intelligent and Sustainable Cloud-based Resource Management and Scheduling

**This repo's contribution:**
- Completes the Phase 1 experiment sequence with the missing 2-VM case
- Introduces Phase 2 to prove that intelligent provisioning — matching VMs exactly to workload — delivers both optimal performance and zero resource waste
- Together, Phase 1 and Phase 2 build the case that sustainable cloud resource management requires matching infrastructure to demand precisely, neither under nor over provisioning
