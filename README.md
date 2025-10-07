# Agentic-Corrective-RAG-worklow
This project will cover a full hands-on workflow and demonstration of how to build an Agentic Corrective RAG (CRAG) System with LangGraph

The idea would be to implement the workflow taking inspiration from the [Corrective Retrieval Augmented Generation](https://arxiv.org/pdf/2401.15884) research paper.

The main challenge of RAG systems include:

- Poor Retrieval can lead to issues in LLM response generation
- Bad retrieval or lack of information in the vector database can also lead to out of context or hallucinated answers

The idea is to couple a RAG system with a few checks in place and perform web searches if there is a lack of relevant context documents to the given user query.
We can build this as an agentic RAG system by having a specific functionality step as a node in the graph and use LangGraph to implement it. Key steps in the node will include prompts being sent to LLMs to perform specific tasks as seen in the detailed workflow below:

![](https://i.imgur.com/oAfXksw.png)


### Agentic Corrective RAG System Workflow

This project implements an **Agentic Corrective RAG System** that enhances the reliability and precision of responses by combining document grading, aumenting web search, and RAG. The system ensures only high-quality, grounded answers are generated even when the initial context retrieved from the vector database is incomplete or irrelevant.

The workflow includes the following components:

1. **Document Retrieval and Grading**:
   - A user query is first sent to a **Vector Database** to retrieve relevant documents.
   - These documents are passed through an **LLM Grader Prompt**:
     - The LLM evaluates each document and labels it as either **'yes'** (relevant) or **'no'** (irrelevant) based on its usefulness for answering the query.
     - This filtering step ensures that only the most relevant documents are retained for response generation.

2. **Dynamic Decision Routing**:
   - A **Decision Node** checks the document grading results:
     - If **> 50% of retrieved documents are relevant**, the system proceeds with a **standard RAG Prompt**, using only those documents to generate an answer.
     - If **<= 50% of retrieved documents are relevant**, the system switches to a **fallback corrective workflow**.

3. **Query Rephrasing and Web Search**:
   - When retrieved context is inadequate:
     - The original query is sent to the LLM with a **Rephrase Prompt** to generate a more search-optimized version of the query.
     - The rephrased query is passed to a **Web Search Tool** to retrieve fresh and more relevant context documents from the web.
     - These web-retrieved documents are then combined with any relevant retrieved context documents from the Vector DB and used as the final context documents

4. **Final Answer Generation**:
   - Whether using documents from the vector database or web search, the final step involves sending the query and relevant context documents into the **RAG Prompt**, which instructs the LLM to:
     - Use only the given documents to answer the question.
     - Avoid making up information or hallucinating unsupported content.

This agentic workflow adds an additional layer of control and recovery to the RAG pipeline, ensuring more accurate responses in dynamic and unpredictable retrieval scenarios.
