# MathsTest2024

![Screensoht of the UI](https://github.com/CAG2Mark/MathsTest2024/assets/55091936/153f0406-9ed7-4ed5-9e64-c434b3c7949b)

The 2024 instalment of my yearly maths test. :P

As usual, this was made as a joke, but regardless, the project serves as a useful framework for making client-side maths tests. The entire system has been revamped this year to allow greater flexibility in setting up questions, and be more user-friendly for the end-user.

# Setting up
Due to the greater flexibility of this new framework, it is a bit more complicated to set up than before.

For each question you want to put in \ your maths test, you need to do the following:
1. Add the question and specify the answer types in `questiondata.json`.
2. Add the sidebar button for that question.
3. Write the page for that question in HTML.

## 1. Adding to `questiondata.json`
In `questiondata.json,` you should add question data to the `questions` array.

Questions contain the following fields:
- `name`: The unique name/identifier of the question. This is only used internally and not visible in the user interface.
- `answers`: The answer fields that the question expects. A question can have multiple answer fields, allowing you to make questions that contain multiple parts.
In JSON, a question looks like:
```json
{
  "name": <question name>,
  "answers": [ <your answer fields here> ]
}
```
Answer fields contain the following fields:
- `name`: The name of the answer field, which must be unique among all other answer fields within the question.
- `type`: What kind of answer is expected. Can be `NUMBER` or `FUNCTION`.
- `isInt`: Whether the answer is expected to be an integer. If it is an integer, then to give the user a failsafe, the user's answer will be rounded to an integer before being checked.
- `inputs` (`FUNCTION` only): The list of input variables to the question.
- `tests` (`FUNCTION` only): The list of test inputs to a function. These test inputs will be fed into the user's function to generate outputs, which will be compared against correct outputs generated by you using the correct answer. Can be numbers or expressions.
An exmaple of a function answer field:
```json
{
  "name": "functionAns",
  "type": "FUNCTION",
  "isInt": false,
  "inputs": ["n", "k"],
  "tests": [
    { "n": 2, "k": 3 },
    { "n": 1, "k": 2 },
    { "n": 4, "k": 10 },
    { "n": "(pi + e) / sin(2)", "k": 4 },
  ]
}
```
## 2. Adding the sidebar button
In `index.html`, add the following item under `question-sidebar-wrap`:
```html
<button data-question-name="YOUR QUESTION NAME" class="question-sidebar-button sidebar-button">
    <span class="inline-circle"></span>
    <span class="question-name">QUESTION TITLE</span>
</button>
```
The question title can be set to anything you like, but will not appear on mobile. The `inline-circle` element will be automatically filled with the question number, which will appear in the **order the buttons appear**.
## 3. Adding the question page
In `index.html`, add the following item under `question-area`:
```html
<div data-question-name="easy" class="main-page question-page content-page">
    <!-- your content -->
</div>
```
Write your question here. KaTeX is installed and any LaTeX equations you write will be automatically rendered.

For each answer field, the question content must contain exactly one answer input box, which can be added using:
```
<div class="math-input" data-answer-name="YOUR ANSWER NAME"></div>
```
It will autuomatically be populated, styled and linked to the question provided you set the question and answer names correctly before.

# Checkpoints
Checkpoints are places where the user can check their answers. Checkpoints check one or more questions at the same time, and by design, to prevent brute-forcing, only provides the following information:
- Whether all answers are correct.
- Whether at least one answer is wrong.
  - If some answer evaluates to NaN or Infinity for a certain input, this information will be conveyed to the user.
- Whether there was an error evaluating a particular answer.
To create a checkpoint, add the following entry under `checkpoints` in `questiondata.json`:
```json
{ 
    "name": "<checkpoint name>", 
    "depends": [ <names of answers this checkpoint will check> ],
    "twiceHash": <sha256 hash of all the answers, twice>
},
```
Note that `name` must be unique. How you obtain `twiceHash` will be explained later. For now, just enter the empty string.

Now, add a checkpoint sidebar button under `question-sidebar-wrap`:
```html
<button data-checkpoint-name="CHECKPOINT NAME" class="checkpoint-sidebar-button sidebar-button">
    <span class="inline-big material-icons">flag</span>
    <span><strong>CHECKPOINT TITLE</strong></span>
</button>
```
You do not need to add a checkpoint page manually. It will be automatically created for you.

To find `twiceHash`, first, add the following line to line `155` of `scripts/questiondata.js`:
```js
console.log(twiceHash);
```
Then, enter all the correct answers for that checkpoint in the UI. Go to the checkpoint page and click `Check Answers`, and the hash will be printed in the console.

# Decrypting Checkpoint Codes
Go to the `Welcome` page and type `decrypt <checkpoint code>` (without the angled brackets). If the code is valid, then the name of the user and which checkpoint the reached will be displayed.