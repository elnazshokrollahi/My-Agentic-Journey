# 🤖 Hello Agent — CSV FAQ Agent

My first AI agent! A simple assistant that answers natural-language questions using **only** the data inside CSV files — never from the model's general knowledge.

Ask it something like *"What are the visiting hours in the hospital?"* and it reads the actual data, finds the right row, and replies in plain English.

> Built as the Week 0 warm-up project for **Applied Agentic AI for SWEs**.

---

## ✨ What it does

- Loads one or more **CSV files** into memory as tables
- Accepts a **plain-English question** from the user
- Figures out which table is relevant, writes and runs **pandas code** against it, and returns the answer
- Stays **grounded in the data** — if the answer isn't in the files, it says so instead of making something up

## 📁 The data

Four sample business datasets (15 rows each):

| File | What's in it |
|------|--------------|
| `saas_docs.csv` | Features, plan limits, and API details for a SaaS product |
| `credit_card_terms.csv` | Card terms — APR, fees, and conditions |
| `hospital_policy.csv` | Hospital rules for visits, records, and admissions |
| `ecommerce_faqs.csv` | Frequently asked questions for an online store |

## 🛠️ Tech stack

- **Python**
- **pandas** — loads each CSV into a DataFrame
- **LangChain** (`langchain-experimental`, `langchain-openai`) — builds the agent: the prompt, the tool, and the reasoning loop
- **OpenAI `gpt-4o-mini`** — the reasoning engine (temperature `0.0` for factual, repeatable answers)
- **Google Colab** — runs entirely in the browser, no local setup

---

## 🧠 How it works

This agent is a smart text-predictor (the model) wrapped in a simple loop (run by LangChain). On every question it cycles through three steps until it has an answer:

1. **Reason** — the model reads the question and decides which table to look in
2. **Act** — it writes a line of pandas code, and the tool runs it for real
3. **Observe** — the result comes back; the model reads it and either runs more code or gives the final answer

```
Question → [ Reason → write pandas → run it → look at result ] → Answer
                ↑__________________________________|
                        (loops until done)
```

The key idea: the model is only shown the **column names** of each table (a 5-row preview), *not* the full data. The actual values are fetched on demand by running code — which is why this approach works even for data far too large to fit in a prompt.

---

## 🚀 How to run

1. **Open the notebook in Google Colab.**

2. **Install the libraries:**
   ```python
   !pip install langchain-experimental langchain-openai pandas requests==2.32.4
   !pip install gdown
   ```

3. **Add your OpenAI API key.** Get one from [platform.openai.com](https://platform.openai.com) (the API needs a small amount of credit added under *Billing*). The notebook prompts for it securely:
   ```python
   from getpass import getpass
   api_key = getpass()
   ```

4. **Create the model and the agent:**
   ```python
   from langchain_openai import ChatOpenAI
   from langchain_experimental.agents.agent_toolkits import create_pandas_dataframe_agent

   llm = ChatOpenAI(model="gpt-4o-mini", temperature=0.0, api_key=api_key)

   agent = create_pandas_dataframe_agent(
       llm,
       dataframes,                 # list of the 4 loaded DataFrames
       verbose=True,               # prints the agent's reasoning loop
       agent_type="openai-functions",
       allow_dangerous_code=True,  # lets the agent run the pandas code it writes
   )
   ```

5. **Ask away:**
   ```python
   response = agent.invoke("What are the visiting hours in the hospital?")["output"]
   print(response)
   ```

---

## 💬 Example questions

```
What is the API rate limit for the free plan?
→ Free tier is limited to 1,000 API requests per day.

What is the return policy for electronics?
→ Electronics can be returned within 30 days in original packaging (15% restocking fee if the seal is broken).

What are the visiting hours in the hospital?
→ Visiting hours are from 10:00 AM to 8:00 PM.
```

Try an **out-of-data** question too (e.g. *"What's the weather today?"*) — the agent should decline rather than guess. That "only answer from the data" behavior is the whole point.

---

## 📚 What I learned

- An **agent** = a model + tools + a loop (reason → act → observe → repeat)
- Grounding answers in a real data source is what stops an AI from making things up
- Every piece has to be **wired explicitly** — a variable existing isn't the same as it being passed to the thing that needs it
- How to read a scary traceback: find the *one real line* and ignore the noise
- `temperature` and the **system prompt** are the two dials that shape an agent's behavior
- What LangChain actually does under the hood: builds the prompt, holds the tool, and runs the loop

## 🗺️ Roadmap / next steps

- [ ] Wrap the agent in a **Streamlit** web UI (file uploader + question box + answer display)
- [ ] Strengthen the system prompt so out-of-data questions are refused even more reliably
- [ ] Let users upload their *own* CSVs at runtime

---

## 🙏 Acknowledgements

Built as the Week 0 mini-project for the **Applied Agentic AI for SWEs** program — a warm-up to get comfortable with agents, tools, and tabular data.
