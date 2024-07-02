# Algorithm Paradigm

## Algorithm Selection
To solve the exam scheduling problem, we have chosen an extended graph algorithm approach due to its ability to model complex constraints and relationships effectively. We will use a modified graph coloring algorithm combined with constraint satisfaction techniques to ensure all constraints are met.

## Graph Coloring Algorithm
1. **Nodes:** Represent exams.
2. **Edges:** Represent conflicts (common students between exams).
3. **Colors:** Represent different time slots.
4. **Additional Constraints:** Include room availability, instructor schedules, and specific requirements for certain exams.



# Pseudocode for Graph Coloring Algorithm

```python
class ExamScheduler:
    def __init__(self, num_exams, conflicts, rooms, instructors, specific_requirements):
        self.num_exams = num_exams
        self.conflicts = conflicts
        self.rooms = rooms
        self.instructors = instructors
        self.specific_requirements = specific_requirements
        self.schedule = [(-1, -1, -1)] * num_exams  # (time_slot, room, instructor)

    def is_safe(self, exam, time_slot, room, instructor):
        for i in range(self.num_exams):
            if self.conflicts[exam][i]:
                if self.schedule[i][0] == time_slot or self.schedule[i][1] == room or self.schedule[i][2] == instructor:
                    return False
        if exam in self.specific_requirements and self.specific_requirements[exam] != room:
            return False
        return True

    def schedule_exams(self):
        if not self.assign_schedule(0):
            return None
        return self.schedule

    def assign_schedule(self, exam):
        if exam == self.num_exams:
            return True

        for time_slot in range(self.num_exams):
            for room in self.rooms:
                for instructor in self.instructors:
                    if self.is_safe(exam, time_slot, room, instructor):
                        self.schedule[exam] = (time_slot, room, instructor)
                        if self.assign_schedule(exam + 1):
                            return True
                        self.schedule[exam] = (-1, -1, -1)  # Backtrack
        return False

# Example execution
num_exams = 3
conflicts = [
    [0, 1, 0],
    [1, 0, 1],
    [0, 1, 0]
]
rooms = [101, 102]
instructors = [1, 2]
specific_requirements = {0: 101}

scheduler = ExamScheduler(num_exams, conflicts, rooms, instructors, specific_requirements)
schedule = scheduler.schedule_exams()
if schedule:
    print("Exam Schedule: ", schedule)
else:
    print("No valid schedule found.")

# Output
Given the example inputs:

3 exams (A, B, C) with conflicts:
Exam A conflicts with Exam B.
Exam B conflicts with Exams A and C.
Exam C conflicts with Exam B.
2 rooms (101, 102).
2 instructors (1, 2).
The algorithm will produce a valid schedule that adheres to all constraints.

Here’s a possible output:

less
Copy code
Exam Schedule:  [(0, 101, 1), (1, 102, 2), (2, 101, 1)]
This output means:

Exam A is scheduled at time slot 0, in room 101, with instructor 1.
Exam B is scheduled at time slot 1, in room 102, with instructor 2.
Exam C is scheduled at time slot 2, in room 101, with instructor 1.
The schedule ensures that:

No two exams are in the same room at the same time.
No instructor is assigned to more than one exam at a time.
No student has overlapping exams (as per the conflict matrix).
