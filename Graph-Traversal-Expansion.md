# 1. HugeGraph Graph Processing: Building New Edge Types Based on Path Start and End Points
## Description

In **big data processing** scenarios, to better utilize the graph database HugeGraph, we propose a new graph processing/extension feature. This feature aims to perform path traversal based on specified rules, and then construct a new edge based on the start and end points of this path.

For example, in a family tree, starting from each vertex, following the path `father->person(male)->father` to the third node, a new edge is established, with the edge type being grandfather.

## Requirement Standards

1. The **start point** can be defined by rules.
2. Implement path traversal based on the specified path.
3. Implement the logic of constructing a new edge from the start to the end of this path.
4. Complete relevant unit tests (UT).
5. Complete the writing of user documentation.

## Technical Requirements

- Familiarity with graph databases/HugeGraph is preferred.
- Possess Java/Scala development capabilities.
- Familiar with basic Linux usage.

## Time Nodes

1. Get started with preliminary use and understanding of `HugeGraph`, and can complete the setup and debugging of the local environment.
2. Design the implementation plan for this feature.
3. Develop the feature and test cases, optimize performance.
4. Improve user documentation.

Note: Difficulty is moderate, workload is moderate.