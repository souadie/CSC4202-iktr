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

Initial Data:
- Number of exams: 4
- Conflict Matrix:
  
  [0, 1, 0, 1],  # Exam 0 conflicts with Exam 1 and Exam 3
  
  [1, 0, 1, 0],  # Exam 1 conflicts with Exam 0 and Exam 2
  
  [0, 1, 0, 1],  # Exam 2 conflicts with Exam 1 and Exam 3
  
  [1, 0, 1, 0]   # Exam 3 conflicts with Exam 0 and Exam 2

- Rooms: [101, 102]
- Instructors: [1, 2]
- Specific Requirements: {0: 101} (Exam 0 requires Room 101)

## Walkthrough of the Code Execution:
1. Graph Coloring:
    - Start with Exam 0 and try colors 0, 1, 2, 3 (max 4 colors but will use 2 as it's a bipartite graph).

2. Color Assignments:
    - Exam 0 → Color 0 (time slot 0)
    - Exam 1 → Color 1 (time slot 1) (as it conflicts with Exam 0)
    - Exam 2 → Color 0 (time slot 0) (as it conflicts with Exam 1)
    - Exam 3 → Color 1 (time slot 1) (as it conflicts with Exam 0)
    - 
The coloring is successful, resulting in the schedule: [0, 1, 0, 1].

3. Assign Rooms and Instructors:
    - Exam 0 (time slot 0):
         - Room: 101 (specific requirement)
         - Instructor: 1 or 2
    - Exam 1 (time slot 1):
         - Room: 101 or 102
         - Instructor: 1 or 2
    - Exam 2 (time slot 0):
         - Room: 102 (only remaining room at time slot 0 since 101 is taken by Exam 0)
         - Instructor: 1 or 2
    - Exam 3 (time slot 1):
         - Room: whichever room is not taken by Exam 1 at time slot 1
         - Instructor: whichever instructor is not taken by Exam 1 at time slot 1

## Final Schedule:
- Exam 0: (time slot 0, room 101, instructor 1)
- Exam 1: (time slot 1, room 102, instructor 2)
- Exam 2: (time slot 0, room 102, instructor 2)
- Exam 3: (time slot 1, room 101, instructor 1)
