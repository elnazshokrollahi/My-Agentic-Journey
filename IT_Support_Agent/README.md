# **Assignment: The Autonomous IT Support Agent**

This is a coding assignment for the **Level 1 IT Incident Responder**.

This assignment assumes the learners have the `openai` library installed and an API key ready and stored in the variable name `OPENAI_APIKEY` as a notebook secret variable and enabled

---

**Objective:** You are building an AI agent that acts as the "first responder" for server incidents. It must:

1. **Investigate:** Check server health and logs when a user reports an issue.
2. **Act:** If CPU is critical (>90%), it should **Restart** the service.
3. **Escalate:** If the issue is complex or logs show a major problem it should **Escalate** to a human.

**Your Task:** complete the code below by filling in the sections marked `### TODO`.
