
// SCRAPBOOK FOR REFACTORING //

#### Form Submission

Application supports number of hooks such as `post-init` or `pre-render`. By adding your handlers there, you can execute certain things between the two major execution steps.

There is one more step in between - form submission. It is used as a call-back into a standard `Form` class. Forms want to parse POST data after they have been fully initialized but before they start rendering.

Forms use unique IDs to determine if they have been submitted, so even if you have multiple forms on the page, they will be able to determine which one have been submitted without your help.

