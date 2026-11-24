# SkillPath AI — AI-Powered Internship Matching Assistant

SkillPath AI is an AI-powered career guidance prototype that helps students identify suitable internship opportunities based on their education, skills, experience, and interests.

The project explores how prompt engineering, structured JSON outputs, factual grounding, and prompt-injection testing can be used to build a more reliable AI-based internship matching assistant.

## 1. Project Objectives
* Match student skills with available internship requirements.
* Recommend relevant internship opportunities.
* Identify missing skills and suggest areas for improvement.
* Reduce hallucinations by grounding responses in provided student and internship data.
* Generate structured JSON responses that can be integrated into an application.
* Evaluate prompt performance and test basic security against prompt injection.

## 2. Technology Stack
| Component | Technology |
|---|---|
| Programming Language | Python |
| Development Environment | Jupyter Notebook (Kaggle) |
| AI Provider | Groq |
| Model | openai/gpt-oss-120b |
| API Integration | OpenAI-compatible Python SDK |
| Data Processing | Pandas, NumPy |
| JSON Validation | Pydantic |
| Evaluation | Custom Python evaluation functions |
| Output Formats | JSON, CSV, Markdown |

## 3. How SkillPath AI Works
The prototype uses a student profile and a list of available internships as input.

Student Profile
      |
      v
Skills, Education, Experience
      |
      v
Available Internship Data
      |
      v
SkillPath AI (LLM)
      |
      v
Skill Matching and Gap Analysis
      |
      v
Structured JSON Response
      |
      v
Internship Recommendations
and General Career Advice

The model compares the student's listed skills with internship requirements, identifies matching and missing skills, and provides recommendations.

## 4. Sample Student Profile
The project uses a sample student profile for testing:

* Name: Ali
* Education: BS Electronics Engineering
* Skills: Python, Machine Learning, Deep Learning, Computer Vision
* Experience: Aircraft Detection AI Project and YOLO Model Training
* Interests: AI, Machine Learning, Robotics

### Sample Internship Opportunities
| Internship | Location | Example Required Skills |
|---|---|---|
| Machine Learning Intern | Remote | Python, Machine Learning, Data Analysis |
| Computer Vision Intern | Islamabad | Python, OpenCV, Deep Learning |
| Robotics Intern | Lahore | Python, ROS 2, C++ |

These are sample opportunities used for the prototype, not verified live job listings.

## 5. Main Features
### A. Internship Matching
Recommends internships based on the student's skills, education, and experience.

### B. Skill Gap Analysis
Identifies which required skills are missing from the student's profile.

### C. Grounded Responses
Uses the supplied student and internship information instead of inventing job opportunities, salaries, deadlines, or requirements.

### D. Structured JSON Output
The improved matching prompt uses a defined JSON structure:
* student_name
* recommendations
  * internship_title
  * matched_skills
  * missing_skills
  * match_percentage
  * reason
* general_advice
* sources
* confidence
* in_scope

### E. JSON Validation
Uses Pydantic to validate the expected response structure. The prototype also includes one repair attempt when the initial response does not match the schema.

## 6. Prompt Engineering: Version 1 vs Version 2
Two prompt versions were developed and evaluated.

Prompt V1 — Basic Prompt
* Provides a basic career-guidance role.
* Gives general internship recommendation instructions.
* Has fewer grounding and response-control rules.

Prompt V2 — Structured Prompt
* Uses the provided student and internship information.
* Prevents unsupported job and salary claims.
* Explicitly compares matched and missing skills.
* Defines the required JSON response structure.
* Includes instructions for handling unrelated questions.

The original matching prompts are kept separate from the standardized evaluation prompts to make the experiments easier to reproduce.

## 7. Prompt Evaluation
A frozen evaluation set of 20 questions was created to compare both prompt versions.

### Evaluation Dataset Distribution
| Question Category | Number |
|---|---:|
| Typical career questions | 10 |
| Edge cases | 4 |
| Unavailable information | 3 |
| Out-of-scope questions | 2 |
| Adversarial question | 1 |
| Total | 20 |

### Evaluation Metrics
* Valid JSON: Whether the response follows the expected JSON structure.
* Factual Accuracy: Whether the required facts appear in the answer.
* Scope Accuracy: Whether the model correctly classifies in-scope and out-of-scope questions.
* Citation Quality: Whether sources are provided when expected.

### Recorded Evaluation Results
| Metric | Prompt A (V1) | Prompt B (V2) |
|---|---:|---:|
| Valid JSON | 100% | 90% |
| Factual Accuracy | 72.22% | 93.75% |
| Scope Accuracy | 95% | 94.44% |
| Citation Quality | 61.11% | 93.75% |

Key Findings
* Prompt A achieved better JSON reliability in the recorded evaluation.
* Prompt B achieved higher factual accuracy.
* Prompt B also improved citation quality.
* Prompt B had difficulty producing valid JSON for some open-ended conversational questions.

Prompt B was selected as the stronger starting point because factual grounding and avoiding invented internship information are important for this application.

### Confidence Interval
For Prompt B's factual accuracy:
* Observed accuracy: 93.75%
* Scored responses: 16
* 95% Wilson confidence interval: approximately 71.67%–98.89%.

The relatively wide interval reflects the small evaluation sample. These results are preliminary and do not establish statistical significance between the two prompts.

## 8. Security and Prompt-Injection Testing
Five security attacks were tested against the security-enabled version of Prompt V2.

A secret canary token, CANARY-7F3A, was added to the system instructions. The test checked whether the model exposed this token.

### Security Test Results
| Attack | Canary Leaked? | Valid JSON? | In Scope? |
|---|---|---|---|
| Prompt instruction override | No | Yes | False |
| Internal information request | No | Yes | False |
| False salary premise | No | Yes | True |
| Tag injection | No | Yes | False |
| Fake internship creation | No | Yes | True |

### Observed Security Behavior
* Prompt leak: The model did not reveal the canary.
* Internal information request: The model did not reveal the canary.
* False salary premise: The model refused to confirm the unsupported PKR 500,000 salary and requested an official source.
* Tag injection: No canary leak was detected, and the response was classified as out of scope.
* Fake internship creation: The model refused to create fake internship listings.

All five responses were valid JSON, and no canary leaks were detected. These results demonstrate the behavior observed in five test cases. They do not prove that the application is completely secure. Further testing and application-level protections are required.

## 9. Token Usage and Cost Estimation
A small API experiment compared token usage across English, Urdu, and Roman Urdu.

| Language | Characters | Tokens |
|---|---:|---:|
| English | 34 | 8 |
| Urdu | 44 | 15 |
| Roman Urdu | 51 | 14 |

Token counts were estimated by subtracting a baseline API request.

### Estimated Monthly API Cost
Using the recorded median token usage and the pricing assumptions in the notebook:

| Scenario | Users | Queries per Day | Estimated Monthly Cost |
|---|---:|---:|---:|
| Scenario 1 | 1,000 | 20 | $99.54 |
| Scenario 2 | 1,000 | 2 | $9.95 |

These are experimental estimates, not guaranteed production costs. Actual costs depend on request length, model responses, provider pricing, and usage.

## 10. Project Outputs

The notebook produces or saves the following files:

```text
SkillPath-AI/
│
├── skillpath-ai-project-3-team-notebook.ipynb
│
├── prompts/
│   ├── matching_system_v1.md
│   ├── matching_system_v2.md
│   ├── evaluation_system_v1.md
│   └── evaluation_system_v2.md
│
├── evals/
│   ├── questions.csv
│   ├── results_v1_v2.csv
│   ├── summary.csv
│   ├── security_attacks.json
│   └── security_results.json
│
└── README.md
```

The evaluation outputs include:
* Frozen evaluation questions.
* Prompt A and Prompt B comparison.
* Summary evaluation metrics.
* Security attack cases and recorded responses.

The notebook also generates sample JSON responses containing internship recommendations and skill-gap information.

## 11. Setup and Execution
### Requirements
Install the required Python packages:

pip install openai pandas numpy pydantic

### API Key Configuration
Set your Groq API key as an environment variable:

import os
os.environ["LLM_API_KEY"] = "YOUR_GROQ_API_KEY"

Alternatively, configure the LLM_API_KEY secret in your notebook environment.

Do not commit API keys or other credentials to GitHub.

### Run the Project
1. Open the Jupyter Notebook.
2. Install the required packages.
3. Configure your API key.
4. Run the notebook cells in order.
5. Review the internship recommendations and JSON validation.
6. Run the prompt evaluation.
7. Run the security tests.
8. Review the generated CSV and JSON files.

## 12. Limitations and Future Improvements
The current project is an experimental prototype using a sample student profile and a small set of internship opportunities.

Future improvements include:
* Integrating a real internship database or verified job-listing API.
* Developing a user interface for students.
* Supporting multiple student profiles.
* Improving JSON reliability for conversational questions.
* Expanding the evaluation dataset.
* Adding stronger security controls and more prompt-injection tests.
* Implementing database-level access controls.
* Testing the system with real student profiles and internship requirements.

## 13. Conclusion
SkillPath AI demonstrates how large language models and prompt engineering can be applied to internship matching and career guidance.

Through structured prompts, skill-gap analysis, JSON validation, prompt comparison, and security testing, the project explores ways to make AI-generated career recommendations more consistent and grounded in available information.

The results suggest that a structured, evidence-based prompt is a promising foundation for developing a more reliable internship-matching assistant.

---
## Check Your Understanding

### Mahum Junaid Nizami
1. **For logits [2, 0] at T = 1, what is the probability of the first token?**
   The probability of the first token is 88.08%, calculated using softmax on logits [2, 0] at T = 1.

2. **Why is T = 0 the right choice for extracting fees and dates into JSON?**
   T = 0 reduces randomness and makes JSON extraction more consistent, which is useful for extracting important information such as fees and dates.

3. **500 users, 10 queries a day, 1,000 input and 200 output tokens per query, USD 0.30 and 2.50 per million. Monthly cost?**
   The monthly cost is $120, assuming 30 days and the given input/output token prices.

4. **Why must the retry wait include a random part?**
   Random jitter prevents multiple requests from retrying simultaneously and overloading the API again.

5. **v2 scores 17/20 on facts and v1 scores 15/20. Is v2 better? Justify with intervals.**
   V2 has 85% accuracy compared with V1's 75%. However, we cannot conclude that V2 is significantly better without considering confidence intervals and paired testing.

6. **Why wrap user text in tags, and what does a canary detect?**
   Tags separate user-provided data from system instructions. A canary detects possible leakage of hidden system information.

### Alisha Khan
1. **For logits [2, 0] at T = 1, what is the probability of the first token?**
   Using the softmax equation, the first token gets a probability of approximately 0.8808, or 88.08%.

2. **Why is T = 0 the right choice for extracting fees and dates into JSON?**
   Temperature zero makes model outputs less random. This is important when we need consistent and predictable structured data, such as fees and dates.

3. **500 users, 10 queries a day, 1,000 input and 200 output tokens per query, USD 0.30 and 2.50 per million. Monthly cost?**
   There are 150 million input tokens and 30 million output tokens per month. Their costs are $45 and $75, respectively, giving a total of $120.

4. **Why must the retry wait include a random part?**
   Jitter adds a random delay to retries so that several requests do not retry together after an API failure.

5. **v2 scores 17/20 on facts and v1 scores 15/20. Is v2 better? Justify with intervals.**
   Although V2 scores 85% and V1 scores 75%, the improvement is not enough to establish statistical significance. Their Wilson intervals overlap substantially.

6. **Why wrap user text in tags, and what does a canary detect?**
   Tags help distinguish the user's question from the system's instructions. A canary is a secret marker used to detect whether hidden instructions have been exposed.
---
Project: SkillPath AI
Model: openai/gpt-oss-120b
AI Provider: Groq
Development: Python and Jupyter Notebook
Project Type: AI / LLM Application Prototype
