# DAT250: Software Technology Experiment Assignment 3
## Singl Page Application

## Project specification

Frontend application built with [Svelte](https://svelte.dev/) and using the API made in [Experiment Assignment 2](https://github.com/Tevuz/DAT250-Assignment2/blob/main/dat250-expass2.md).

<b>Goal</b>: Make two user interface components:
-  [One for creating new polls](/app/src/lib/poll_create.svelte).
-  [One for viewing and voting on polls](/app/src/lib/poll_list.svelte).

[App.svelte](/app/src/App.svelte) is the entrypoint for the applications and handles routing between the views.

## Remarks 

- Svelte was choosen for this project and a lot of time was spent learning the framework. 
- Adjustments have been made to the model such that [Test Scenario 1](/src/test/TestScenario1.http) from the previous assignment runs. Further tests are still needed to cover all actions and should be automated. 
