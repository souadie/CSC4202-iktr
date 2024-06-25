# Algorithm Paradigm

## Algorithm Selection
We chose the Constraint Satisfaction Problem (CSP) approach because it is systematic and flexible, capable of handling complex constraints effectively.

## Pseudocode
```python
define variables: exams, rooms, timeslots
define domains: available rooms and timeslots for each exam
define constraints:
  for each pair of exams:
    if exams share students:
      they cannot be scheduled at the same timeslot
  for each exam and room:
    room must be available
    room must meet capacity and special requirements
  for each instructor:
    instructor can only supervise one exam at a time
initialize CSP solver
solve CSP
if solution exists:
  return schedule
else:
  return "No feasible schedule"
