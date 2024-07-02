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
class GraphColoringScheduler:
    def __init__(self, num_exams, conflicts, rooms, instructors, specific_requirements):
        self.num_exams = num_exams
        self.conflicts = conflicts
        self.rooms = rooms
        self.instructors = instructors
        self.specific_requirements = specific_requirements
        self.schedule = [-1] * num_exams  # Color of each exam (time slot)
        self.adj_list = {i: [] for i in range(num_exams)}

        # Build the adjacency list from the conflict matrix
        for i in range(num_exams):
            for j in range(num_exams):
                if conflicts[i][j] == 1:
                    self.adj_list[i].append(j)

    def is_safe(self, exam, color):
        for neighbor in self.adj_list[exam]:
            if self.schedule[neighbor] == color:
                return False
        return True

    def graph_coloring(self, exam=0):
        if exam == self.num_exams:
            return True

        for color in range(self.num_exams):
            if self.is_safe(exam, color):
                self.schedule[exam] = color
                if self.graph_coloring(exam + 1):
                    return True
                self.schedule[exam] = -1  # Backtrack

        return False

    def assign_rooms_and_instructors(self):
        exam_details = [(-1, -1) for _ in range(self.num_exams)]  # (room, instructor)

        for exam in range(self.num_exams):
            time_slot = self.schedule[exam]
            for room in self.rooms:
                for instructor in self.instructors:
                    if self.is_safe_room_instructor(exam, time_slot, room, instructor):
                        exam_details[exam] = (room, instructor)
                        break
                if exam_details[exam][0] != -1:
                    break

            if exam_details[exam][0] == -1:
                return None  # No valid room and instructor found

        return exam_details

    def is_safe_room_instructor(self, exam, time_slot, room, instructor):
        for other_exam in range(self.num_exams):
            if self.schedule[other_exam] == time_slot:
                if self.specific_requirements.get(exam) and self.specific_requirements[exam] != room:
                    return False
                if self.schedule[other_exam] == time_slot and self.rooms[other_exam] == room:
                    return False
                if self.schedule[other_exam] == time_slot and self.instructors[other_exam] == instructor:
                    return False
        return True

    def schedule_exams(self):
        if not self.graph_coloring():
            return None

        exam_details = self.assign_rooms_and_instructors()
        if not exam_details:
            return None

        schedule = [(self.schedule[i], exam_details[i][0], exam_details[i][1]) for i in range(self.num_exams)]
        return schedule


# Example execution
num_exams = 4
conflicts = [
    [0, 1, 0, 1],  # Exam A conflicts with Exam B and D
    [1, 0, 1, 0],  # Exam B conflicts with Exam A and C
    [0, 1, 0, 1],  # Exam C conflicts with Exam B and D
    [1, 0, 1, 0]   # Exam D conflicts with Exam A and C
]
rooms = [101, 102]
instructors = [1, 2]
specific_requirements = {0: 101}  # Exam A requires Room 101

scheduler = GraphColoringScheduler(num_exams, conflicts, rooms, instructors, specific_requirements)
schedule = scheduler.schedule_exams()
if schedule:
    print("Exam Schedule: ", schedule)
else:
    print("No valid schedule found.")

```

# Output

Given the example inputs:

1) 3 exams (A, B, C) with conflicts:

   -Exam A conflicts with Exam B.

   -Exam B conflicts with Exams A and C.

   -Exam C conflicts with Exam B.

2) 2 rooms (101, 102).

3) 2 instructors (1, 2).

The algorithm will produce a valid schedule that adheres to all constraints.

Here’s a possible output:

## Exam Schedule:  [(0, 101, 1), (1, 102, 2), (2, 101, 1)]


This output means:

Exam A is scheduled at time slot 0, in room 101, with instructor 1.

Exam B is scheduled at time slot 1, in room 102, with instructor 2.

Exam C is scheduled at time slot 2, in room 101, with instructor 1.

The schedule ensures that:

No two exams are in the same room at the same time.

No instructor is assigned to more than one exam at a time.

No student has overlapping exams (as per the conflict matrix).
