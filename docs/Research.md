# Research

## RAPTOR: Recursive Abstractive Processing For Tree-Organized Retrieval
Source: https://arxiv.org/pdf/2401.18059.pdf

### TL;DR
Raptor implements a so called Raptor Tree. Instead of just chunking data and retrieving from these chunks, Raptor clusters similar Chunks together and generates summaries of those clusters that are reembedded to create a tree-structure. Upon receiving a query, Raptor traverses the tree and selects relevant summaries and chunks (Leafs of the tree). This allows the LLM to both have high and low level understanding of the context. Problematic is that they rely on a clustering alghorithm and an LLM to create the clusters and the summaries that form the higher level context. If these methods fail, the LLM might not receive relevant chunks, because their roots were never chosen by the tree traversing alghortithm

### Implementation and Usefullness for us
In the context of code there could be a possible implementation for a higher level context of TC Projects. The leafs would be single project files like POUs, DUTs, etc. and the roots could be the description of the Project as a whole (Even more abstraction is possible). 

For an RAG-System of Function-Blocks, this method seems unfeasable, because of it's automatic clustering and summary generation. If the Function-Blocks get clustered manually and higher level constructs are set (e.g Libraries), then this could be an option, althougth this would be a lot of work. 

