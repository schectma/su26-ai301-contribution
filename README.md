# Contribution 1: teamcolors for teams >9

**Contribution Number:** [1]  
**Student:** Alex Schectman  
**Issue:** [#4123](https://github.com/wesnoth/wesnoth/issues/4123)  
**Status:** [Phase II] [In-Progress]

---

## Why I Chose This Issue

This issue is a simple enhancement that serves as a baby step into graphical asset management for a game. Stated felt difficulty is clear, and the desired outcome is straightforward enough to let any given contributor immediately hone in on a well-defined aspect of the codebase. I also have a fondness for tactical strategy games, so there's an element of genuine personal interest.

---

## Understanding the Issue

### Problem Description

Only nine side/unit colors are in use, but at least six more are available and would be useful.

### Expected Behavior

Sides/units created in quantities up to 15 should have unique identifying colors.

### Current Behavior

Sides/units creates in quantities between nine and 15 share the same color (teal).

### Affected Components

Scenario setup and team management: `wesnot/src/team.cpp`
Others:
```
wesnot/src/game_initialization/connect_engine.cpp
wesnot/src/game_config.cpp
```

---

## Reproduction Process

### Environment Setup

OS: Ubuntu 24.04 in WSL 2.6.1 on Windows 11 Home 25H2 26200.8655.
Official INSTALL.md offers sparse instructions for Windows setup but overly-general instructions for Linux. I opted to attempt the latter with AI assistance, if for no other reason than to ensure CLI commands and navigation remained simple. Initially I'd tried to use my Ubuntu 22.04 install, but that didn't support SDL3 (packages unavailable for that version), so I had to install 22.04. Following that, I used Copilot to guide me through running test suites, then I launched the game to verify that it built correctly. wml_tests kept timing out but all boost_tests succeeded and the game ran properly.
I'm using VS Code with a remote connection to the local repo directory in WSL to work on files. 

### Steps to Reproduce

1. Add a new valid campaign, map, scenario, and main config file to the ./data/campaigns/ directory in the code/text editor. Terrain should be as simple as possible (just plain grass or dirt) and side/unit count should be 16.
2. Launch the game by executing ./wesnoth in the CLI while pwd is the root of the repo.
3. Click "Campaign", scroll down to and click the newly-added campaign, then click "Play".
4. Observe population of map with 16 units, the first nine (typically counting from the left) of which sit atop a uniquely colored disc/halo. All units beyond the first nine should sit atop the same color (teal).

### Reproduction Evidence

- **Commit showing reproduction:** [fa0e05d](https://github.com/schectma/wesnoth/commit/fa0e05d23d8b5db25d5f9cffb467deb9ddec436e)
- **Screenshots/logs:**
<img width="1592" height="774" alt="Screenshot 2026-06-12 153348" src="https://github.com/user-attachments/assets/4ecc208e-51d0-404c-a91e-56e7cc93e8bf" />
<img width="952" height="578" alt="image" src="https://github.com/user-attachments/assets/9e18929e-019b-4949-ae3d-ca11448fb000" />

- **My findings:** Sides are capped at nine by design so any number beyond that can only be defined in custom campaign files. This helped me realize that there's likely a formula somewhere in the C++ code that directly maps each side to a color by index (or similar instance indicator). With the help of AI, I began to trace the color assignment pipeline and learned that it spans at least a couple files. I also learned that WML and the game's use of it is fairly easy to grasp and use (the official wiki and forums are great). It also seems that the actual color assignment logic is flexible enough to accommodate what is effectively whitelisting of existing (but unused) side/unit colors in a config file. 

---

## Solution Approach

### Analysis

Several colors available in team-colors.cfg are simply not made available for use by the default color rotation logic. 

### Proposed Solution

Enable those colors for use in the standard rotation of those assignable.

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** Only sides 1-9 get distinct colors. Any sides beyond that share an identical color. The contributor who proposed this as an issue wants to expand that range of unique colors to 15. 

**Match:** Color definitions in team-colors.cfg use gate flags for activation.

**Plan:** [Step-by-step implementation plan]
1. [Modify file X to do Y]
2. [Add function Z]
3. [Update tests]

**Implement:** [Link to your branch/commits as you work]

**Review:** [Self-review checklist - does it follow the project's contribution guidelines?]

**Evaluate:** [How will you verify it works?]

---

## Testing Strategy

### Unit Tests

- [ ] Test case 1: [Description]
- [ ] Test case 2: [Description]
- [ ] Test case 3: [Description]

### Integration Tests

- [ ] Integration scenario 1
- [ ] Integration scenario 2

### Manual Testing

[What you tested manually and results]

---

## Implementation Notes

### Week [X] Progress

[What you built this week, challenges faced, decisions made]

### Week [Y] Progress

[Continue documenting as you work]

### Code Changes

- **Files modified:** [List]
- **Key commits:** [Links to important commits]
- **Approach decisions:** [Why you chose certain approaches]

---

## Pull Request

**PR Link:** [GitHub PR URL when submitted]

**PR Description:** [Draft or final PR description - much of the content above can be adapted]

**Maintainer Feedback:**
- [Date]: [Summary of feedback received]
- [Date]: [How you addressed it]

**Status:** [Awaiting review / Iterating / Approved / Merged]

---

## Learnings & Reflections

### Technical Skills Gained

[What you learned technically]

### Challenges Overcome

[What was hard and how you solved it]

### What I'd Do Differently Next Time

[Reflection on your process]

---

## Resources Used

- [Link to helpful documentation]
- [Tutorial or Stack Overflow post that helped]
- [GitHub issues or discussions that helped]
