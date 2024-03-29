# Graph Traversal Expansion

## Description

***Subtitle: Building New*** ***Edge*** ***Types Based on Path Start and End Vertex***

In **big data** **processing** scenarios, to better utilize the **graph database HugeGraph**, we propose a new **graph processing/extension function**. This function aims to perform **path traversal** based on specified rules, and then build a **new edge** based on the start and end points of this path.

For example, in a **family tree**, starting from each vertex, following the path `father->person(male)->father` to the third node, a new edge is established, and the type of edge is **grandfather**.

## Requirement Standards

1. The **starting point** can be defined by rules. (refer [traverser-api](https://hugegraph.apache.org/docs/clients/restful-api/traverser/#3216-template-paths))
2. Implement path **traversal** based on the specified path.
3. Implement the logic of building a **new edge** from the start to the end of this path. (API)
4. Complete relevant **unit tests (UT)**.
5. Complete the writing of **user documentation**.

## Technical Requirements

- Familiarity with **graph databases**/HugeGraph is preferred.
- Possess **Java/Scala** development capabilities & familiar with basic **Linux** usage.
- Understand **data structures and algorithms**, especially those related to graphs.
- Have good problem-solving abilities and teamwork skills.

## Time Nodes

1. Start to use and understand `HugeGraph` preliminarily, and can complete the setting and debugging of the **local environment**.
2. Design the **implementation plan** for this function.
3. Develop functions and **test cases**, optimize performance.
4. Improve **user documentation**.

- **Difficulty:** Easy~Medium
- **Project size:** ~130 hour (part-time/medium)