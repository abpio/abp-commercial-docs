## FrmForms

FrmForms Table is used to store forms.

### Description

This table stores information about the forms. Each record in the table represents a form and allows to manage and track the forms effectively. This table is important for managing and tracking the forms of the application.

### Module

[`Volo.Form`](../forms.md)

---

## FrmQuestions

FrmQuestions Table is used to store form questions.

### Description

This table stores information about the form questions. Each record in the table represents a form question and allows to manage and track the form questions effectively. This table is important for managing and tracking the form questions of the application.

### Module

[`Volo.Form`](../forms.md)

### Used By

| Table | Column | Description |
| --- | --- | --- |
| [`FrmChoices`](#frmchoices) | ChoosableQuestionId | To match the form choice with the form question. |

---

## FrmChoices

FrmChoices Table is used to store form choices.

### Description

This table stores information about the form choices. Each record in the table represents a form choice and allows to manage and track the form choices effectively. This table is important for managing and tracking the form choices of the application.

### Module

[`Volo.Form`](../Form/form.md)

### Uses

| Table | Column | Description |
| --- | --- | --- |
| [`FrmQuestions`](#frmquestions) | Id | To match the form choice with the form question. |

---

## FrmFormResponses

FrmFormResponsesm Table is used to store form responses.

### Description

This table stores information about the form responses. Each record in the table represents a form response and allows to manage and track the form responses effectively. This table is important for managing and tracking the form responses of the application.

### Module

[`Volo.Form`](../forms.md)

### Used By

| Table | Column | Description |
| --- | --- | --- |
| [`FrmAnswers`](#frmanswers) | FormResponseId | To match the form answer with the form response. |

---

## FrmAnswers

FrmAnswers Table is used to store form answers.

### Description

This table stores information about the form answers. Each record in the table represents an form answer and allows to manage and track the form answers effectively. This table is important for managing and tracking the form answers of the application.

### Module

[`Volo.Form`](../forms.md)

### Uses

| Table | Column | Description |
| --- | --- | --- |
| [`FrmFormResponses`](#frmformresponses) | Id | To match the form answer with the form response. |