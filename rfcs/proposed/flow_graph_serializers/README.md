# Dining philosophers node

Various flow-graph nodes accept a constructor argument that specifies the maximum concurrency the graph can assume for that particular node.
For example, in high-energy physics, an algorithm might reconstruct physics tracks from tracks recorded by an experiment's detector.
If the algorithm is not threadsafe, a reasonable node construction could be:

``` c++
function_node<Hits, Tracks> track_maker{g, tbb::flow::serial, [](Hits const&) -> Tracks {...}};}
```

where the `tbb::flow::serial` argument constrains the flow graph to execute the node body by no more than one thread at a time.

There are cases, however, where specifying a concurrency limit of `tbb::flow::serial` is insufficient to guarantee thread-safety of the full graph.
In particular, a thread-unsafe resource shared between two or more node bodies should not be accessed by more than one thread at a time.


- We can never lose input data

> Short description of the idea proposed with explained motivation.
>
> The motivation could be:
> - Improved users experience for API changes and extensions. Code snippets to
>   showcase the benefits would be nice here.
> - Performance improvements with the data, if available.
> - Improved engineering practices.
>
> Introduction may also include any additional information that sheds light on
> the proposal, such as history of the matter, links to relevant issues and
> discussions, etc.

## Proposal

Different token types that can carry state.

``` c++
auto& gpu_resource = tbb::flow::serialized_resource<GPU>(g, 2);
auto& root_resource = tbb::flow::serialized_resource<ROOT>(g, 1);

function_node<
  tuple<Hits, Calib>,
  Tracks,
  tuple<GPU, ROOT>> fn{g,
                       make_tuple(gpu_resource, root_resource),
                       [](tuple<Hits, Calib> const&, tuple<GPU, ROOT>) -> Tracks { ... }
                      };

```

> A full and detailed description of the proposal with highlighted consequences.
>
> Depending on the kind of the proposal, the description should cover:
>
> - New use cases supported by the extension.
> - The expected performance benefit for a modification.
> - The interface of extensions including class definitions or function
> declarations.
>
## Implementation experience

## Future work

> A proposal should clearly outline the alternatives that were considered,
> along with their pros and cons. Each alternative should be clearly separated
> to make discussions easier to follow.
>
> Pay close attention to the following aspects of the library:
> - API and ABI backward compatibility. The library follows semantic versioning
>   so if any of those interfaces are to be broken, the RFC needs to state that
>   explicitly.
> - Performance implications, as performance is one of the main goals of the library.
> - Changes to the build system. While the library's primary building system is
>   CMake, there are some frameworks that may build the library directly from the sources.
> - Dependencies and support matrix: does the proposal bring any new
>   dependencies or affect the supported configurations?
>
> Some other common subsections here are:
> - Discussion: some people like to list all the options first (as separate
>   subsections), and then have a dedicated section with the discussion.
> - List of the proposed API and examples of its usage.
> - Testing aspects.
> - Short explanation and links to the related sub-proposals, if any. Such
>   sub-proposals could be organized as separate standalone RFCs, but this is
>   not mandatory. If the change is insignificant or doesn't make any sense
>   without the original proposal, you can have it in the RFC.
> - Execution plan (next steps), if approved.

## Open Questions

> For new proposals (i.e., those in the `rfcs/proposed` directory), list any
> open questions.
