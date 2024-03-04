# Research

## RAG

### RAPTOR
RAPTOR: Recursive Abstractive Processing For Tree-Organized Retrieval

Source: [RAPTOR](https://arxiv.org/pdf/2401.18059.pdf)

#### TL;DR
Raptor implements a so called Raptor Tree. Instead of only chunking data and retrieving from these chunks, Raptor clusters similar Chunks together and generates summaries of those clusters which are then reembedded to create a tree-structure. Upon receiving a query, Raptor traverses the tree, choosing the next branch based on the query and selects relevant summaries and chunks (Leafs of the tree). This allows the LLM to both have high and low level understanding of the context. Problematic is that they rely on a clustering alghorithm and an LLM to create the clusters and the summaries that form the higher level context. If these methods fail, the LLM might not receive relevant chunks, because their roots were never chosen by the tree traversing alghortithm

#### Implementation and Usefullnes for us
In the context of code there could be a possible implementation for a higher level context of TC Projects. The leafs would be single project files like POUs, DUTs, etc. and the roots could be the description of the Project as a whole (Even more abstraction is possible). 

For an RAG-System of Function-Blocks, this method seems unfeasable, because of it's automatic clustering and summary generation. If the Function-Blocks get clustered manually and higher level constructs are set (e.g Libraries), then this could be an option, althougth this would be a lot of work. 

### AnyTool
AnyTool: Self-Reflective, Hierarchical Agents for Large-Scale API Calls

Source: [AnyTool](https://arxiv.org/pdf/2402.04253.pdf)

#### TL;DR
Implements a hierarchical tree structure similarly to [Raptor](#raptor). But instead of building such a tree, they introduce a novel approach to traverse an existing tree using LLMs. At each level they operate with agents. These agents receive the user query and have a set of functions they are able to call. The higher level agents can get a list of all the elements in a sub-category or the description/s of spefific elements/s. Upon choosing a subcategory, the agent can deploy a new agent that operates on the new lower level. The low-level agents can add APIs to an API Pool. The Agent can then choose to have the API checked. A solver will call the API and try to solve the user's query. In a self-reflection process, another Agent checks wether the query has been solved, or wether the search should continue.

#### Implementation and Usefullnes to us
Looking further than an API-Implementation, this hierarchical structure could be used for code, function blocks and many other implementations.

The difficulty with implementing such a construct is the sheer amount of compute needed even for a simple task. There are multiple agents running in parallel only to retrieve feasible options plus an agent to solve and check the results. This makes it nearly impossible to run locally at reasonable sppeds and the cost of running this directly from an inference provider would be quite high.