# data_science
## Overview

This project analyzes a dataset of user messages sourced from LiveChat and Telegram. Each record contains:
- **id_user:** Unique identifier for the user.
- **timestamp:** The time the feedback was received.
- **source:** The platform (LiveChat or Telegram).
- **message:** The user’s message (e.g., “I am unable to access the site from the US.”).

First we classify these messages, extract insights, and then we talk to a chatbot that can answer natural language queries with meaningful answers. The chatbot can filter messages by category, time range, and source, and it retains conversational context to provide a seamless user experience.

## Objectives

- **Message Classification:**
- Categorize messages into 
    - deposit_issues
    - withdrawal_issues
    - bonus_issues
    - game_issues
    - account_issues
    - login_issues
    - payment_issues
    - technical_issues
    - other_issues


- **Chatbot Design:**
  - **Dynamic Filtering:** Allows filtering of messages by category, time range, and source.
    - *Example queries:*
      - “Telegram deposit issues in the previous month?”
      - “LiveChat game issues in the last month?”
  - **Conversational Memory:** Retain context from previous queries. For instance, if a user initially requests “Telegram deposit issues in the previous month” and then asks “What about LiveChat?”, the chatbot updates only the source while preserving the category and timeframe.
  - **User Interface:** Provides a simple interactive UI (via a notebook widget interface) for chatbot interactions instead of relying solely on terminal input.

- **Statistical Insights & Graphical Analysis :**
  - Provides key metrics such as the number of messages per category, source, or timeframe.
  -  Detects spikes in a specific category within a given timeframe and highlight seasonal trends using interactive graphs.

## Setup & Usage Instructions

1. **Prerequisites:**
   - Python 3.12 or higher.
   - Install dependencies with:
     ````ipynb
     %pip install Insert_Library
     ````

2. **Running the Application:**
   - Open `classification_and_chatbot.ipynb` in Jupyter Notebook.
   - Execute all the cells and analyze the data.
   - Do not run this two lines unless you  want to wait 37 minutes for the model to train,  and spend a lot of openAi API credits
    ```
    df = load_and_classify_data("Data.csv")
    df.to_csv('data_classified.csv', index=False)
    ```
   - If using the interactive UI for comunicating with the chatbot, run the last cell to start the chatbot.

## Evaluation Questions & Answers

### 1. How did you classify feedback?
TUKAJ RABIM FIXAT
- **Methodology:**  
  We used a **hybrid approach** combining rule-based logic with manual review:
  - **Rule-Based:** Predefined keywords and patterns guide the initial categorization.  
  - **Machine Learning (Optional):** A model may be applied to flag ambiguous cases.  
- **Advantages:**  
  - Fast initial classification with clear, actionable rules.
  - Flexibility to incorporate statistical models for edge cases.
- **Drawbacks:**  
  - Rule-based methods may miss nuances.
  - Machine learning requires annotated data and may misclassify vague feedback.
- **Handling New Issues:**  
  For previously unseen issues (e.g., a new wallet blocking deposits), the system flags messages with low confidence scores for manual review. This feedback is then used to update rules and retrain the model.

### 2. How does your chatbot manage conversational context?
- The chatbot retains **session-level memory**:
  - Stores key parameters (e.g., category, time range, source) from previous queries.
  - On follow-up queries, if parameters are omitted (like changing only the source), it uses the remembered context to ensure consistent filtering.
  - This is achieved using Python dictionaries or session objects within the application environment.

### 3. What are the main limitations?

- **Misclassification**  
  The hard-coded rules (word-based) misclassified messages as other_issues. So I switched to openAI API. It however is not perfect, and misclassifies messages as some issue, when it should classify as other_issues. I believe it could be improved by renaming the 
- **Model Training Time:**
  Classifying the texts model can be time-consuming, especially with large datasets.
- **Vague Feedback:**  
  Some messages are ambiguous and may not clearly indicate the issue. This falls under the category of "other_issues."
- **Multi-Category Overlaps:**  
  A single message might fit into multiple categories, I found the messages are mostly in one category. Almoust no one wrote long messages with multiple issues.

### 4. How could the system be improved?
- **Enhanced NLP Models:**  
  Integrate advanced models (e.g., transformer-based classifiers) to better handle nuanced language.
- **Dynamic, Multi-label Classification:**  
  Allow messages to belong to multiple categories where appropriate.
- **Robust Conversational Memory:**  
  Implement more sophisticated context tracking (e.g., using context vectors or a database for conversation history).
- **User Feedback Loop:**  
  Incorporate user corrections to continuously refine classification accuracy.

### 5. How does the chatbot track and utilize past queries to refine current requests?
- The chatbot logs each user query along with its associated parameters (category, timeframe, source).
- On subsequent queries, it accesses the log to:
  - Pre-fill missing parameters.
  - Adjust the query by merging previous context with new input.
  - This allows incremental refinements (for instance, changing only the source while keeping the category/time range constant).

### 6. If the full conversation were available, would you approach this task differently?
- **Yes.**  
  With a full conversation context:
  - A more sophisticated **dialog system** could be employed.
  - We could use context-aware NLP models to understand the full dialogue, rather than treating each query in isolation.
  - This approach could involve dialogue management frameworks to better capture nuances and long-term dependencies across messages.

### 7. How would you measure and validate the correctness of message classifications?
- **Metrics:**  
  - Calculate precision, recall, and F1-score using a labeled test dataset.
  - Perform cross-validation for machine learning models.
- **Validation:**  
  - Manual review of a sample of classified messages.
  - Compare against historical data trends and known issue distributions.
  - Use user feedback to adjust and recalibrate the classification rules or model thresholds.

---
