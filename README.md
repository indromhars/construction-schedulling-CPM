# Vue 3 + Vite

This template should help get you started developing with Vue 3 in Vite. The template uses Vue 3 `<script setup>` SFCs, check out the [script setup docs](https://v3.vuejs.org/api/sfc-script-setup.html#sfc-script-setup) to learn more.

Learn more about IDE Support for Vue in the [Vue Docs Scaling up Guide](https://vuejs.org/guide/scaling-up/tooling.html#ide-support).
# construction-schedulling-CPM

# CPM Project Scheduler

## Overview

**CPM Project Scheduler** is a web-based application designed to assist project managers and teams in planning, managing, and analyzing project schedules using the **Critical Path Method (CPM)**. This method identifies the most critical sequence of activities in a project, which determines its minimum completion time. 

The application uses principles of **Discrete Mathematics** to calculate dependencies, scheduling metrics, and critical paths, making it a powerful tool for structured project management.

---

## Features

1. **Add Project Activities**:
   - Input details like activity name, duration, time units, and dependencies.

2. **Calculate Scheduling Metrics**:
   - Determine:
     - **Earliest Start (ES)**
     - **Earliest Finish (EF)**
     - **Latest Start (LS)**
     - **Latest Finish (LF)**
     - **Float (slack time)**

3. **Identify Critical Activities**:
   - Activities with `Float = 0` lie on the critical path and must be completed on time to prevent project delays.

4. **Project Visualization**:
   - The tool helps users visualize dependencies and scheduling flexibility.

---

## Discrete Mathematics Components

The **Critical Path Method (CPM)** heavily relies on discrete mathematics concepts, including:

1. **Directed Graphs**:
   - Each project activity is represented as a **node** in a directed graph.
   - Dependencies between activities form **edges** connecting these nodes.

2. **Path Analysis**:
   - The goal is to identify the **longest path** (critical path) in the graph, where the duration of activities determines the overall project completion time.

3. **Algorithmic Calculations**:
   - The **Earliest Start (ES)** and **Earliest Finish (EF)** are calculated based on the forward traversal of the graph.
   - The **Latest Start (LS)** and **Latest Finish (LF)** are calculated using backward traversal.
   - **Float** is derived as `LS - ES` or `LF - EF`.

4. **Dependency Representation**:
   - The project schedule is modeled as a **partially ordered set (poset)** of activities where dependencies enforce precedence relations.

---

## Algorithm Used

The CPM Project Scheduler implements a **Topological Sorting Algorithm** to process the directed acyclic graph (DAG) of project activities. 

### Key Steps:
1. **Forward Pass**:
   - Traverse the graph to calculate **ES** and **EF** for each activity.
2. **Backward Pass**:
   - Traverse the graph in reverse order to calculate **LS** and **LF**.
3. **Float Calculation**:
   - Compute the slack time (float) for each activity as `LS - ES`.

### Is CPM part of Discrete Mathematics?
Yes, CPM involves discrete mathematics as it works with:
   - Finite sets of activities and dependencies.
   - Graph-based modeling of projects.
   - Algorithmic analysis of paths and scheduling metrics.

---

## How This Project Uses Discrete Mathematics

- **Finite State Analysis**: Activities and dependencies are finite, making them suitable for graph representation.
- **Graph Theory**: Directed acyclic graphs (DAGs) model dependencies and enable scheduling analysis.
- **Algorithmic Thinking**: Topological sorting and path analysis algorithms are core components.
- **Optimization**: The critical path minimizes the project duration.

---

## How to Use the Application

1. **Add Activities**:
   - Provide details about each activity, including its name, duration, and dependencies.

2. **Calculate the Critical Path**:
   - Click the "Calculate Critical Path" button to perform CPM analysis.

3. **Review Results**:
   - View ES, EF, LS, LF, float, and critical path status for each activity.

4. **Modify as Needed**:
   - Add, update, or remove activities and recalculate the schedule.

5. **Clear Data**:
   - Use the clear button to reset all activities and start a new analysis.

---

## Installation
To install and run the CPM Project Scheduler, follow these steps:

Prerequisites
Ensure you have Node.js (v14 or later) and npm installed on your machine.
Steps
Clone the repository:

```bash
git clone <repository-url>
cd construction-schedulling
```

Install dependencies:

```bash
Salin kode
npm install
```

Run the development server:

```bash
Salin kode
npm run dev
```

This will start the application on http://localhost:3000.

Build for production: To create a production-ready build:

```bash
Salin kode
npm run build
```

Preview the build: After building the application, you can preview it using:

```bash
Salin kode
npm run preview
``` 

## Conclusion

The CPM Project Scheduler is a practical tool for project scheduling and demonstrates the application of **Discrete Mathematics** concepts such as graph theory and algorithmic optimization. By automating calculations, the tool empowers teams to manage projects more efficiently while offering a deeper understanding of the mathematical principles driving project scheduling.