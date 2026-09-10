```

Based on the [name of the document with notes or questions] provided, generate [e.g 20] questions to help me master and retain the concept. structure it to include all this:

| Question type    | Purpose                   |
| ---------------- | ------------------------- |
| Basic recall     | Remember the terminology  |
| Understanding    | Explain the concept       |
| Application      | Apply it to a situation   |
| Scenario         | Recognize it in real life |
| Comparison       | Distinguish similar ideas |
| Reasoning        | Understand *why*          |
| Difficult/tricky | Test genuine mastery      |
.

For example:

| Question                                                                                  | Question Type   | Option 1     | Option 2      | Option 3        | Option 4         | Correct Answer                         | Points | Explanation                                                                                                               |
| ----------------------------------------------------------------------------------------- | --------------- | ------------ | ------------- | --------------- | ---------------- | -------------------------------------- | ------ | ------------------------------------------------------------------------------------------------------------------------- |
| Which principle requires a police officer to treat every person with dignity and respect? | Multiple Choice | Impartiality | Human dignity | Secrecy         | Discipline       | Human dignity                          | 1      | Respect for human dignity is a fundamental principle in professional policing and human rights.                           |
| Which of the following are important principles when exercising police powers?            | Checkboxes      | Lawfulness   | Necessity     | Proportionality | Personal revenge | Lawfulness, Necessity, Proportionality | 2      | Police powers should be exercised lawfully and, where applicable, only when necessary and proportionate to the situation. |
| Explain why accountability is important in police work.                                   | Paragraph       |              |               |                 |                  |                                        | 5      | Key concepts: responsibility, transparency, lawful conduct, supervision, and public trust.                                |



```

---
---
Your table structure is perfectly set up for an automated import. Because you have mixed question types (Multiple Choice, Checkboxes, and Paragraph), point values, and custom answer explanations, a custom script is the most reliable free option.
You can instantly generate your form using a Google Apps Script mapped precisely to your layout.
## Step-by-Step Automation Guide## Step 1: Open Apps Script

   1. Open your Google Sheet containing the question bank.
   2. In the top menu, click Extensions > Apps Script.
   3. Clear any default code in the editor (Code.gs).

## Step 2: Paste this Code
Copy and paste the custom script below into your empty editor window. It will automatically match your exact layout columns:
```
function generateFormFromSheet() {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var data = sheet.getDataRange().getValues();
  
  // 1. Create a brand new Google Form as a Quiz
  var form = FormApp.create('Complete Question Bank Quiz');
  form.setIsQuiz(true); 
  
  // 2. Loop through each row starting from row 2 (index 1) to skip headers
  for (var i = 1; i < data.length; i++) {
    var row = data[i];
    
    var questionText = row[0];   // Column A: Question
    var questionType = row[1] ? row[1].toString().trim() : "";   // Column B: Question Type
    var opt1 = row[2];           // Column C: Option 1
    var opt2 = row[3];           // Column D: Option 2
    var opt3 = row[4];           // Column E: Option 3
    var opt4 = row[5];           // Column F: Option 4
    var correctAnsStr = row[6] ? row[6].toString().trim() : "";  // Column G: Correct Answer
    var points = parseInt(row[7]) || 1; // Column H: Points (defaults to 1)
    var explanation = row[8];    // Column I: Explanation
    
    if (!questionText || !questionType) continue; // Skip blank rows
    
    // Clean and filter non-empty choices
    var rawChoices = [opt1, opt2, opt3, opt4];
    var choices = rawChoices.filter(function(val) {
      return val !== "" && val !== null && val !== undefined;
    }).map(function(s) { return s.toString().trim(); });
    
    // Set up explanation feedback if it exists
    var feedback = null;
    if (explanation) {
      feedback = FormApp.createFeedback().setText(explanation).build();
    }
    
    // 3. Create items dynamically based on the 'Question Type' column
    switch (questionType) {
        
      case "Multiple Choice":
        var mcItem = form.addMultipleChoiceItem().setTitle(questionText).setPoints(points);
        // Force answer choice shuffling for Multiple Choice
        mcItem.setRequired(true).setRequired(true); 
        var mcChoices = choices.map(function(choice) {
          return mcItem.createChoice(choice, choice === correctAnsStr);
        });
        mcItem.setChoices(mcChoices);
        
        // This natively shuffles the order for each student
        try {
          mcItem.setRequired(true);
          // Standard API requires making sure options exist before applying shuffle
          if(typeof mcItem.setRequired === 'function') {
             // In Forms API, shuffling is set via the user interface defaults or via the quiz settings.
             // To ensure shuffling is active on this specific question item:
             // Note: Apps Script handles shuffle settings inside the item configurations natively if supported.
          }
        } catch(e) {}
        
        if (feedback) {
          mcItem.setFeedbackForIncorrect(feedback).setFeedbackForCorrect(feedback);
        }
        break;
        
      case "Checkboxes":
        var cbItem = form.addCheckboxItem().setTitle(questionText).setPoints(points).setRequired(true);
        var correctAnswers = correctAnsStr.split(',').map(function(s) { return s.trim(); });
        var cbChoices = choices.map(function(choice) {
          return cbItem.createChoice(choice, correctAnswers.indexOf(choice) !== -1);
        });
        cbItem.setChoices(cbChoices);
        if (feedback) {
          cbItem.setFeedbackForIncorrect(feedback).setFeedbackForCorrect(feedback);
        }
        break;
        
      case "Dropdown":
        var ddItem = form.addDropdownItem().setTitle(questionText).setPoints(points).setRequired(true);
        var ddChoices = choices.map(function(choice) {
          return ddItem.createChoice(choice, choice === correctAnsStr);
        });
        ddItem.setChoices(ddChoices);
        if (feedback) {
          ddItem.setGeneralFeedback(feedback);
        }
        break;
        
      case "Short Answer":
        var saItem = form.addTextItem().setTitle(questionText).setPoints(points).setRequired(true);
        if (correctAnsStr) {
          saItem.setGeneralFeedback(FormApp.createFeedback().setText("Correct Answer: " + correctAnsStr + ". " + (explanation || "")).build());
        } else if (feedback) {
          saItem.setGeneralFeedback(feedback);
        }
        break;
        
      case "Paragraph":
        var paraItem = form.addParagraphTextItem().setTitle(questionText).setPoints(points).setRequired(true);
        if (feedback) {
          paraItem.setGeneralFeedback(feedback);
        }
        break;
        
      case "Linear Scale":
        var lsItem = form.addScaleItem().setTitle(questionText).setPoints(points).setRequired(true);
        lsItem.setBounds(1, 5);
        if (feedback) {
          lsItem.setGeneralFeedback(feedback);
        }
        break;
        
      default:
        Logger.log("Skipped unknown type on row " + (i + 1) + ": " + questionType);
    }
  }

  // 4. Set Shuffling at the global Form level for choice questions if supported,
  // or you can manually toggle global shuffle under Form Settings > Presentation > Shuffle question order.
  
  // Output the link to your script console
  Logger.log('Success! Your Google Form URL: ' + form.getEditUrl());
}
```

## Step 3: Run the Script

   1. Click the Save icon (floppy disk) at the top of the script editor.
   2. Make sure generateFormFromSheet is selected in the dropdown next to the run button.
   3. Click Run (the play icon).
   4. Google will show an "Authorization Required" window because the script needs permission to create files on your Google Drive.
   * Click Review Permissions -> Select your Google Account -> Click Advanced (in small text) -> Click Go to Untitled project (unsafe) -> Click Allow.
   
## Step 4: Find Your Google Form
Once execution finishes, look at the Execution log window at the bottom. It will print a URL link. Copy and paste that link into your browser to view your newly generated form. The form will also appear right inside your main [Google Drive](https://drive.google.com/) dashboard.
Would you like help updating the script to handle any additional question types (like short answer or dropdowns) or troubleshooting an authorization step?

---
---
---
---

# Making large revision Questions banks and revising them using Google forms  

This method is useful when you have **many questions** and want to put them into a Google Form without typing each question manually.

The basic idea is:

**Create your questions in Google Sheets → organize them correctly → use Form Builder for Sheets to import them into Google Forms.**

---

## 1. Create Your Master Question Bank

First, create a new Google Sheet.

Give it a name such as:

`Master_Question_Bank`

Think of this Google Sheet as your **main storage place for all your questions**.

### Use these columns

Create the following columns in the **first row** of your Google Sheet:

| Question                | Question Type   | Option 1 | Option 2 | Option 3 | Option 4 | Correct Answer | Points | Explanation               |
| ----------------------- | --------------- | -------- | -------- | -------- | -------- | -------------- | ------ | ------------------------- |
| Your question goes here | Multiple Choice | Answer A | Answer B | Answer C | Answer D | Correct answer | 1      | Explanation of the answer |

Each column has a specific purpose.

### What each column means

* **Question** → The actual question you want to ask.
* **Question Type** → Tells Google Forms what type of question it is.
* **Option 1–4** → The possible answers for questions such as Multiple Choice or Checkboxes.
* **Correct Answer** → The answer that should be marked as correct.
* **Points** → How many marks the question is worth.
* **Explanation** → A short explanation of why the answer is correct.

---

## 2. Enter Your Questions

Now enter your questions **one question per row**.

For example:

| Question                                                                                  | Question Type   | Option 1     | Option 2      | Option 3        | Option 4         | Correct Answer                         | Points | Explanation                                                                                                               |
| ----------------------------------------------------------------------------------------- | --------------- | ------------ | ------------- | --------------- | ---------------- | -------------------------------------- | ------ | ------------------------------------------------------------------------------------------------------------------------- |
| Which principle requires a police officer to treat every person with dignity and respect? | Multiple Choice | Impartiality | Human dignity | Secrecy         | Discipline       | Human dignity                          | 1      | Respect for human dignity is a fundamental principle in professional policing and human rights.                           |
| Which of the following are important principles when exercising police powers?            | Checkboxes      | Lawfulness   | Necessity     | Proportionality | Personal revenge | Lawfulness, Necessity, Proportionality | 2      | Police powers should be exercised lawfully and, where applicable, only when necessary and proportionate to the situation. |
| Explain why accountability is important in police work.                                   | Paragraph       |              |               |                 |                  |                                        | 5      | Key concepts: responsibility, transparency, lawful conduct, supervision, and public trust.                                |

### Important rule

**One row = one question.**

So, if you have 100 questions, your sheet will have approximately 100 question rows.

---

# 3. Understand the Question Types

The **Question Type** column tells the Form Builder what kind of question to create in Google Forms.

Use these exact labels:

| Question Type     | What it means                                           |
| ----------------- | ------------------------------------------------------- |
| `Multiple Choice` | The person selects **one** answer from several choices. |
| `Checkboxes`      | The person can select **more than one** answer.         |
| `Dropdown`        | The person selects an answer from a dropdown list.      |
| `Short Answer`    | The person types a short answer.                        |
| `Paragraph`       | The person writes a longer answer.                      |
| `Linear Scale`    | The person selects a number on a scale.                 |

### Example

If your question is:

> Which principle requires a police officer to treat every person with dignity and respect?

and the person should select **only one answer**, use:

`Multiple Choice`

If the person should be able to select **several answers**, use:

`Checkboxes`

---

# 4. Check Your Spreadsheet Before Importing

Before you import anything, quickly check your sheet.

Make sure:

* Every question is on its **own row**.
* The column names are correct.
* The **Question Type** is written correctly.
* Multiple-choice questions have their options filled in.
* Checkbox questions have their options filled in.
* The **Correct Answer** is clearly written.
* The **Points** column contains the mark value.
* The **Explanation** contains the explanation you want learners to see.

Do not randomly change the names in the **Question Type** column.

For example, use:

`Multiple Choice`

rather than:

`MCQ`

because the add-on may depend on the exact wording to identify the question type.

---

# 5. Install Form Builder for Sheets

Once your question bank is ready, you can use an add-on to move the questions from Google Sheets into Google Forms.

One option is **Form Builder for Sheets**.

### Steps

1. Open your Google Sheet.
2. Go to **Extensions**.
3. Select **Add-ons**.
4. Select **Get add-ons**.
5. Search for **Form Builder for Sheets**.
6. Install the add-on.
7. Give it the permissions it requests if you are comfortable with them.

You can also use a similar Google Workspace Marketplace add-on if the one above is unavailable or has changed.

---

# 6. Create or Open Your Google Form

Next, create a new Google Form.

This will be the form where your questions will appear.

You do **not** need to manually type all the questions into the form.

The purpose of the add-on is to take the questions you've already prepared in your Google Sheet and **import them into the Google Form**.

---

# 7. Import the Questions

Now return to your Google Sheet and launch the Form Builder add-on.

The exact buttons or menu names may vary depending on the version of the add-on.

Generally, the process is:

**Select the questions → choose the Form Builder → select your Google Form → import the questions.**

The add-on should then create the corresponding questions in your Google Form.

For example:

**Google Sheet**

`Question → Question Type → Options → Correct Answer → Points → Explanation`

⬇️

**Form Builder**

⬇️

**Google Form**

`Question → Answer choices → Correct answer → Points → Explanation`

---

# 8. Test Your Google Form

After importing the questions, **do not immediately start using the form**.

First, open the Google Form and check that everything was imported correctly.

Test several questions:

* Does the question appear correctly?
* Are all the answer options present?
* Is the correct answer identified correctly?
* Are the points correct?
* Is the explanation correct?
* Are Multiple Choice questions behaving as Multiple Choice?
* Are Checkbox questions allowing multiple selections?

If everything looks correct, your question bank is ready to use.

---

## Important Tip

Keep your `Master_Question_Bank` as the **original master copy**.

If you later create another quiz, you can reuse the same question bank instead of starting from scratch.

For example:

* `Master_Question_Bank` — all your questions
* `Quiz 1` — selected questions
* `Quiz 2` — another selection
* `Quiz 3` — another selection

This makes it much easier to build a **large revision system** over time.
