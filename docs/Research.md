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

## Frameworks

### DSPy (Work in Progress)
Framework for "Programming" with LLMs

Source: [DSPy](https://dspy-docs.vercel.app/)

#### TL;DR
DSPy provides pre-written modules to create tasks like **Chain-of-Thought** or **Question-Answering**. One of their big advantages is that one can add depth to a program with a very similar to Layers in PyTorch (It also uses syntax inspired by PyTorch). Instead of having one prompt to do a whole task, a task can be divided into smaller tasks to provide better results, plus certain classes can be reused for similar tasks. The main advantage of DSPy though is what they call their compiler. Once you have finished building your pipeline, it can be compiled with DSPy. Internally, this will build and improve the prompt to achieve better results. Also, it is then possible to create short, generic prompts and not needing to write a specific prompt for every usecase:
```python
class BasicQA(dspy.Signature):
    """Answer questions with short factoid answers."""

    question = dspy.InputField()
    answer = dspy.OutputField(desc="often between 1 and 5 words")
```
The compiler will change the prompt to the necessary usecase and will either use the description or the name of the field (here: `question`)
To add steps like Chain-of-Thought, it is as simple as initiating a CoT Module:
```python
class GenerateAnswer(dspy.Signature):
    """Answer questions with short factoid answers."""

    context = dspy.InputField(desc="may contain relevant facts")
    question = dspy.InputField()
    answer = dspy.OutputField(desc="often between 1 and 5 words")

class RAG(dspy.Module):
    def __init__(self, num_passages=3):
        super().__init__()

        self.retrieve = dspy.Retrieve(k=num_passages)
        self.generate_answer = dspy.ChainOfThought(GenerateAnswer)

    def forward(self, question):
        context = self.retrieve(question).passages
        prediction = self.generate_answer(context=context, question=question)
        return dspy.Prediction(context=context, answer=prediction.answer)
```
The `dspy.ChainOfThought(GenerateAnswer)` will add a Chain-of-Thougth prompt automatically during compilation, signigfically improving the quality

#### Implementation and Usefullnes to us
As this is a whole framework, this would dictate the way we would use LLMs in the future. But this framework is very powerful and promising. It is on the other hand quite complex and needs a lot of getting into, especially when using it's wide feature set. Seeing the performance that this framework can deliver even on very complex tasks, I think that it's worth looking into.

#### Work in Progress
The current form of the framework is very powerful when it works and new features get added regularly. But it is definatly unfished. I was not able to recreate most of their "tutorial" code and a lot of the provided examples are quite hard to read as they mix a lot of their functionalities with other existing frameworks. 