# Experiencegift front-end assessment

The goal of this exercise is to assess candidates front-end development skills.

Please note that there are multiple correct ways to implement a solution for this assignment.

## Design mock-up

You can find the design mock-up for the assignment below:

https://xd.adobe.com/view/b4805488-95c7-4b09-ba94-6fd01139756e-e1d3/

## Assignment Description

At Experiencegift, we're building a form that allows users to search for hotel rooms. A search query can have the following parameters:

- Destination
- Arrival and departure
- Guests

The assignment proposed is to implement the search form as shown in the design mock-up provided above.

The data for the "Destination" field fetched from ```destinations.json``` file provided.

The allowed dates for selection are 2 days from today as minimum and one year in the future as maximum. The maximum range that can be selected is 30 days.

The maximum number of rooms is 4 and the maximum number of total guests (including children) is 9. At least one adult is required for this field to be valid. Number of children per room must not exceed the number of adults per room.

Upon submission all selected data must be collected and saved in a JavaScript object that can be logged to the browser's console. 

### What we're expecting:

- UI matching the design provided
- Responsive layout
- Auto-complete functionality for "Destination" field
- Implementation of date picker
- Passengers selection functionality
- Validation of all form fields before submission

### What we're NOT expecting:
- Any action after form submission besides logging the collected data.

## Stack

You are free to use any front-end technologies that you see fit for completing the assignment.


## Submission
Please clone the repository and create a private repository on your own account. Then, create a new branch and submit a Pull Request with your proposed solution. Make sure to add and request review on the PR of the following github users:

- @GeorgeExperiencegift


**Good luck!**
