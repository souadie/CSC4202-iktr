# Algorithm Paradigm

## Algorithm Selection
To solve the exam scheduling problem, we have chosen graph algorithms due to their ability to model complex constraints and relationships effectively. We will use graph coloring to ensure no two exams with common students are scheduled at the same time.


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
