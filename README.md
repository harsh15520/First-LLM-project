### Project Overview
A compact conversational pipeline that converts a natural‑language function description into a documented, testable Python file. The script prompts the user, instructs an LLM to generate code, extracts and cleans the code, requests documentation and tests, sanitizes a filename, and saves the final artifact.

---

### Quick Start
- **Set API key**: export `GROQ_API_KEY` in your environment.  
- **Run**: `python your_script.py`  
- **Interactive flow**: enter a function description when prompted. The script prints intermediate outputs and saves a `.py` file containing the documented function and tests.

---

### Architecture at a Glance
```
User → messages → generate_response(completion) → raw response → extract_code_block → append assistant → follow‑ups → save file
```
- **messages** holds the conversation history with roles `system`, `user`, and `assistant`.  
- **generate_response** calls `completion()` with `model`, `messages`, and `max_tokens`.  
- **extract_code_block** isolates code from Markdown using `split('```')`, selects `[1]`, `.strip()`, and trims a `python` tag if present.  
- **develop_custom_function** orchestrates prompts, cleans outputs, and writes the final file.

---

### Key Functions and Responsibilities
- **generate_response(messages)**  
  - Acts as the connector to the model.  
  - Supplies model address, payload, and generation limits.  
  - Returns the raw assistant text from `response.choices[0].message.content`.

- **extract_code_block(response)**  
  - Extracts the first Markdown code block.  
  - Pattern: `split('```')` → `[1]` → `.strip()` → remove `python` tag.  
  - Returns clean, runnable code.

- **develop_custom_function()**  
  - Interactive orchestrator that: prompts user, sets `system` role, sends user prompt, cleans code, appends assistant output, requests documentation and tests, sanitizes filename, and saves the `.py` file.

---

### Design Rationale and Quick Notes
- **Why `completion()`**: abstracts HTTP, headers, JSON, endpoints, and parsing so you write one line instead of plumbing.  
- **Why roles**: `system` sets persona, `user` gives tasks, `assistant` stores prior outputs. The model has no memory outside `messages`.  
- **Why `max_tokens=1024`**: balanced default for code plus explanation. Tune per use case.  
- **Why split/strip pattern**: minimal, dependency‑free extraction of Markdown code blocks.

---
