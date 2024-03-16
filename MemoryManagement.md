## **Background**

When the JVM GC performs a large amount of garbage collection, the latency of the request is often high, and the response time becomes uncontrollable. To reduce request latency and response time jitter, the hugegraph-server graph query engine has already used off-heap memory in most OLTP algorithms. However, at present, hugegraph cannot control memory based on a single request Query, so a Query may exhaust the memory of the entire process and cause OOM, or even cause the service to be unable to respond to other requests. To solve this problem, we can implement a memory management module based on a single Query.

## **Project Output Requirements**

- Implement a unified memory pool, independently manage JVM off-heap memory, and adapt the memory allocation methods of various native collections, so that the memory mainly used by the algorithm comes from the unified memory pool, and it is returned to the memory pool after release.
- Each request corresponds to a unified memory pool, and the memory usage of a request can be controlled by counting the memory usage of a request.
- Complete related unit tests UT and basic documentation.

## **Key Technology Analysis and Selection**

1. Where will a large amount of memory be occupied?
    1. When querying/inserting data, the allocated vertex, edge, attribute objects (optional requirements, but the priority is high P2)
    2. Cache memory (optional requirement P3)
    3. When serializing/deserializing (including the receiving buffer read from storage), the allocated byte array (optional requirement P3)
    4. Collection objects used in graph algorithms (required requirement P1)
    5. Collection objects in Gremlin query group, dedup and other operators (optional requirement P3)
    6. Objects allocated by REST interface requests (optional requirement P4)
2. Whether to use the dependent library for memory segment page management, how to choose?
    1. Whether to choose Netty memory management library? --rpc depends on the netty library, and you can also refer to the postgres memory management module
    2. Ensure that large, medium, and small memories can be efficiently managed
    3. Ensure that memory restrictions can be made according to Query instances
    4. May need to use a memory object pool (such as Vertex object pool, this requirement is not yet certain, see whether it is necessary to completely replace Vertex with a binary access object)
3. How to manage memory at multiple levels: system memory, multiple request memory, memory at each stage of a single request, temporary memory?
    1. Adopt MemoryContext tree structure?
4. How to unify the interface for allocating memory everywhere?
    1. Each Query has an Allocator instance, and one Allocator corresponds to multiple MemoryContexts
    2. How to pass the Allocator to each place, or put it in the context? (Analysis: If it is placed in the parameters, the transformation cost may be relatively large; it is recommended to put it in the threadlocal context, but cross-thread transmission needs to be considered)
    3. How to get the corresponding Allocator for return when manually releasing objects
    4. When the JVM automatically releases the wrapped object, it needs to release the data object itself, how to get the corresponding Allocator for return
5. Is memory concurrency safe between multiple threads?
    1. Ensure that multi-threaded concurrent access is safe
    2. Ensure that multi-threaded concurrent release is safe
6. How to release memory as quickly as possible
    1. Clearly, the memory that the subsequent step does not need to use can be released as soon as possible
    2. Temporary memory used in a loop can be applied from a separate MemoryContext
    3. When the Query ends, release all objects allocated by the corresponding Allocator
7. Module portability
    1. In principle, the memory management module itself does not depend on HugeGraph's code
    2. Plan to be able to smoothly port to HugeGraph-Computer in the future
8. Statistics and monitoring (optional)
    1. Statistics on memory usage
    2. Statistics on the memory usage of various objects
    3. Statistics on which objects and usage are leaking memory

## **Overall Design**

Overall, it can be divided into 3 modules:

1. Memory management implementation module. Implement the life cycle management of memory objects, memory capacity restrictions and other functions, and provide the Allocator interface (including allocation, release interface). This is a relatively independent module.
2. Integrate the Allocator module into the HugeGraph context and provide a unified interface for memory transformation.
3. Transform the places where a large amount of memory is occupied, and adapt to use Allocator for object allocation and release.

## Detailed Design

TODO

Points to consider:

1. Research options
2. Layering and sub-module division
3. Interface definition: create Allocator interface, use interface