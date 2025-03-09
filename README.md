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
   ```
    #Write API KEY HERE
    openai.api_key = "sk-proj-7 ...
    ```
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
  - used OpenAI API to classify messages into predefined categories.
  - The function builds a prompt that explains the task to the GPT-3.5-turbo model. This prompt lists all allowed categories (from the variable CATEGORIES) and instructs the model:  
    · "Given a user's message, classify it into one of these categories," and  
    · "If it doesn't match any category well, classify it as other_issues."  
  - The prompt also includes the user’s message.  
  - The model then generates a reply based on the given instructions.
  - After receiving the response, the function formats (trims and lowercases) the output and checks if it exactly matches one of the allowed categories.
  - If the response doesn't match an expected category—perhaps due to a slight variation in wording or a new, unseen issue—the function sets the category as "other_issues." This ensures that any feedback that isn't clearly recognized is still captured.

  - **Why openAi:**  
  - Rule-based methods missed nuances.
  - Machine learning requires annotated data, which was not available.
  - OpenAI API provides a pre-trained model

- **Why It Did (or Didn’t) Classify as Expected:**  
  - **Successes:**  
    · The method works well for messages that fit clearly within the provided categories. The API can often grasp the context and assign the correct label.  
  - **Limitations / Misclassification:**  
 
    - the model misclassifies messages that are mentioning something, but not have a clear issue. For example, a message like "I love the new game" might be classified as game_issues, even though it's not a problem.

  ```
  "Whaaaa I only have one account",account_issues

  "on that first page i lost 8 and won 2",game_issues

  "Just right now I only played 100 spins not 200 and I won 489.30 but I only played 100 spins",game_issues
  ```
- **How New Issues Would Be Seen:**  

  - If a new type of issue arises (e.g., a new wallet blocking deposits) the openAI model will  classyfy it as deposit_issues
  - The prompt enforces that any message which doesn’t clearly match one of the known categories is assigned to "other_issues."

### 2. How does your chatbot manage conversational context?
- The chatbot retains **session-level memory**:
  - Stores key parameters (e.g., category, time range, source) from previous queries.
  - On follow-up queries, if parameters are omitted (like changing only the source), it uses the remembered context to ensure consistent filtering.

### 3. What are the main limitations?

- **Misclassification**  
  The hard-coded rules (word-based) misclassified messages as other_issues. So I switched to openAI API. It however is not perfect, and misclassifies messages as some issue, when it should classify as other_issues.
- **Model Training Time:**
  Classifying the texts model can be time-consuming, especially with large datasets. Just this part of the data takes 37 minutes to run.
- **Vague Feedback:**  
  Some messages are ambiguous and may not clearly indicate the issue. This falls under the category of "other_issues."
- **Multi-Category Overlaps:**  
  A single message might fit into multiple categories, I found the messages are mostly in one category. Almoust no one wrote long messages with multiple issues.

### 4. How could the system be improved?

- **Better prompt:** 
    I believe it could be improved by renaming the category from other_issues to other, and then retraining the model. I However do  not know how many credits I am allowed to use and I do not want to spend all of them, and be left with no credits for the demonstration.
- **Robust Conversational Memory:**  
    Implement more sophisticated context tracking. For instance, store all the previous queries and responses in a session as one conversation. And classify conversations instead of individual messages.
- **Enhanced NLP Models:**  
    Use more advanced NLP models Gpt-4 or o3-mini, to improve classification accuracy.



### 5. How does the chatbot track and utilize past queries to refine current requests?
The chatbot logs each user query along with its associated parameters (category, timeframe, source). On subsequent queries, it accesses the log to:
- Pre-fill missing parameters.
- Adjust the query by merging previous context with new input.
- This allows incremental refinements (for instance, changing only the source while keeping the category/time range constant).

Examples:

1. User query: Telegram deposit issues in the first 1 day of November 2024  
  Log output: {'category': 'deposit_issues', 'source': 'telegram', 'count': 1, 'unique_users': 1}

2. User query: in the first 11 days of November 2024  
  Log output: {'category': 'deposit_issues', 'source': 'telegram', 'count': 9, 'unique_users': 9}

3. User query: What about LiveChat?  
  Log output: {'category': 'deposit_issues', 'source': 'livechat', 'count': 24, 'unique_users': 24}

4. User query: Now show me game issues  
  Log output: {'category': 'game_issues', 'source': 'livechat', 'count': 41, 'unique_users': 41}


### 6. If the full conversation were available, would you approach this task differently?
- **Yes. Then I could write the program to answer questions lik this** 

**User:**  
"Can you combine the results and summarize the trends for both deposit and bonus issues across both platforms for November?"

**Chatbot:**  
"Certainly. Overall in November:  
- **Deposit Issues:**  
  - Telegram: 120 messages, spike on November 15th  
  - LiveChat: 95 messages, generally steady  
- **Bonus Issues (LiveChat last week):**  
  - 20 messages, minor peak on November 28th  

It appears that Telegram is experiencing a higher volume of deposit complaints compared to LiveChat, while bonus issues on LiveChat seem to concentrate towards the end of the month.

  With a full conversation context:
  - A more sophisticated **dialog system** could be employed.


### 7. How would you measure and validate the correctness of message classifications?

- **Manual Review:**  
  - First, save your classified data into a file such as `data_classified.csv`.
  - Then, manually review a sample (or the entire file) and create a ground truth file (e.g., `data_classified_truth.csv`) where you mark the correct category for each message.
  - Compare the classifications in `data_classified.csv` with those in the truth set to identify misclassifications.

- **Metrics Calculation:**  

  - **Precision:**  
    The proportion of true positives among all predicted possitives
    **Formula:** Precision = TP / (TP + FP)
  
  - **Recall:**  
    The proportion true positives, that were correctly identified by the system (TP + false negatives, FN).  
    **Formula:** Recall = TP / (TP + FN)
  
  - **F1-Score:**  
    The harmonic mean of precision and recall, providing a balanced measure of accuracy.  
    **Formula:** F1 = 2 * (Precision * Recall) / (Precision + Recall)

---
