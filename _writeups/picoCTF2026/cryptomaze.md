---
layout: default
title: "picoCTF 2026: cryptomaze"
level: "Intermediate"
description: "Solving an encrypted pathfinding puzzle using BFS and bitwise XOR logic."
category: "picoCTF-Crypto"
---

## Description
You are trapped in a digital maze. Every door is locked with a bitwise challenge. Find the path to the exit to recover the interstellar map coordinates.

## Analysis
The 'maze' is represented as a graph where each node is a 16-byte hex string. To move between connected nodes, the transition must satisfy a specific XOR condition. This is a classic **Breadth-First Search (BFS)** problem combined with cryptographic checks. 



## Solution
I wrote a Python script to treat the maze as a graph. I used a queue to explore all possible paths, applying the XOR decryption at each step to see which "neighboring" room was the valid next step.

### Python Exploit Script:
```python
import collections

# Simplified maze representation: {room_id: [neighbors]}
maze_data = {
    "start": ["room1", "room2"],
    "room1": ["room3"],
    # ... more rooms
}

def solve_maze(start_node, target_node):
    queue = collections.deque([(start_node, [start_node])])
    visited = set()

    while queue:
        (current_node, path) = queue.popleft()
        if current_node == target_node:
            return path

        if current_node not in visited:
            visited.add(current_node)
            for neighbor in maze_data.get(current_node, []):
                # In the real challenge, we perform an XOR check here:
                # if (int(current_node, 16) ^ int(neighbor, 16)) == MAGIC_KEY:
                queue.append((neighbor, path + [neighbor]))

    return None

path = solve_maze("start", "exit")
print(f"Path Found: {' -> '.join(path)}")