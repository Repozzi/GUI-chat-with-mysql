# GUI-chat-with-mysql 

Here is a project of building a natural language SQL chatbot using Mixtral model (via GroqCloud). This project guides you through the development of a chatbot that can interpret natural language queries, generate SQL queries, and fetch results from a SQL database. It utilizes the power of Mixtral model (Mistral AI), integrated with a Streamlit GUI for an enhanced interaction experience.

![chain-architecture](https://github.com/Repozzi/GUI-chat-with-mysql/assets/139656027/fe79860b-f187-4888-88b7-30f75d83c6d3)


## Brief Explanation of How the Chatbot Works

The chatbot works by taking a user's natural language query, converting it into a SQL query using Mixtral model, executing the query on a SQL database, and then presenting the results back to the user in natural language. This process involves several steps of data processing and interaction with the GroqCloud API and a SQL database, all seamlessly integrated into a Streamlit application.

As you can see in the diagram above, we will first create a SQL chain that will generate SQL queries based on the user’s input. We will then create a LangChain chain that will allow us to chat with the database using natural language. We will use the LangChain package to create both chains.

We have to install MySQL connector package, before dealing with database.

```bash
pip install langchain mysql-connector-python 
```
### Create SQL chain
Once we install corresponding packages we can create SQL chain. 
We have to provide LLM with database schema and the user’s input to generate the SQL query:

```bash
from langchain_core.prompts import ChatPromptTemplate

template = """Based on the table schema below, write a SQL query that would answer the user's question:
{schema}

Question: {question}
SQL Query:"""
prompt = ChatPromptTemplate.from_template(template)
```

### Create the full Chain

Now that we have the SQL chain, we can create the full chain that will allow us to chat with the database using natural language. We will need to create a new prompt template that takes:

- the SQL query generated by the SQL chain
- the response from the database to the SQL query
- the user’s input

```bash
template = """Based on the table schema below, question, sql query, and sql response, write a natural language response:
{schema}

Question: {question}
SQL Query: {query}
SQL Response: {response}"""
prompt_response = ChatPromptTemplate.from_template(template)
```

Now we can generate full chain providing required inputs 

```bash
full_chain = (
    RunnablePassthrough.assign(query=sql_chain).assign(
        schema=get_schema,
        response=lambda vars: run_query(vars["query"]),
    )
    | prompt_response
    | model
)
```

### Streamlit usage

We will use Streamlit as an interactive application. We have to provide corresponding configuration to app: sidebar, buttons and chat history.

To launch the Streamlit app and interact with the chatbot:
```bash
streamlit run app.py
```

## Conclusion

This application allows us to chat with a MySQL database using Python and LangChain through the GUI (Streamlit). We used the LangChain wrapper of SQLalchemy to interact with the database. We also used the LangChain package to create a custom chain that allowed us to chat with the database using natural language. Thank you for reading!






