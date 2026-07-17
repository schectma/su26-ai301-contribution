# Contribution 1: teamcolors for teams >9

**Contribution Number:** [1]  
**Student:** Alex Schectman  
**Issue:** [#4123](https://github.com/wesnoth/wesnoth/issues/4123)  
**Status:** [Phase I / Phase II / Phase III / **<ins>Phase IV</ins>**] [**<ins>In Progress</ins>** / Complete]

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

Color definitions: `wesnoth/data/core/team-colors.cfg`
Scenario setup and team management: `wesnoth/src/team.cpp`
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

- **My findings:** Sides are capped at nine by design so any number beyond that can only be defined in custom campaign files. This helped me realize that there's likely a formula somewhere in the C++ code that directly maps each side to a color by index (or similar instance indicator). With the help of AI, I began to trace the color assignment pipeline and learned that it spans at least a couple files. I also learned that WML and the game's use of it is fairly easy to grasp and use (the official wiki and forums are great). It also seems that the actual color assignment logic is flexible enough to accommodate what is effectively whitelisting of existing (but unused) side/unit colors in a config file. Finally, I realized that the default color assigned to sides past the current limit is simply the last eligible (furthest down from top of file) color listed in the config file.

---

## Solution Approach

### Analysis

Several colors available in `team-colors.cfg` are simply not made available for use by the default color rotation logic. Default color assignment is handled by a specific function in `team.cpp` but it just can't handle more than the original nine colors. Minimap reds are all literally the same rgb hex value.

### Proposed Solution

Enable those colors for use in the standard rotation of those assignable. Overload that default color assignment function to just use the last available color. Pick a few close but visually distinguishable reds.

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** Only sides 1-9 get distinct colors. Any sides beyond that share an identical color. The contributor who proposed this as an issue wants to expand that range of unique colors to 15. They also want one of those new colors to be the fallback color and want reds on the minimap to be easier to tell apart.

**Match:** Color definitions in `team-colors.cfg` use gate flags for activation. `get_side_color_id()` in `team.cpp` uses a hardcoded string. Red rgb hex values have a single purpose and can safely be altered.

**Plan:**
1. Add `default=yes` to the definition of each color specified in the original issue. Add "lightred" to `get_side_color_id()` along with logic that checks for it. Experiment with and propose a few new minimap reds.

**Implement:** [fix-issue-4123](https://github.com/schectma/wesnoth/tree/fix-issue-4123)

**Review:** Contribution guidelines have not been violated. Proposed fix is extremely minimal and based on another user's/contributor's acknowledged valid suggestion.

**Evaluate:** Verification will use the same new campaign added for reproduction. Will just launch the game and visually confirm that all sides on the map have a distinct color.

---

## Testing Strategy

### Unit Tests

Boost tests: https://github.com/wesnoth/wesnoth/blob/master/run_boost_tests
There are WML tests but they all time out. This doesn't seem to have any effect on runtime outcomes or other tests.

### Integration Tests

CI pipeline includes [tests](https://github.com/wesnoth/wesnoth/tree/master/utils/CI) for various platforms as well as for WML (proprietary markup language) formatting.

### Manual Testing

Visual confirmation of color changes in running game. What I did certainly had the intended effect (although it wasn't ideal, as I found out afterward).

---

## Implementation Notes

### Week [1] Progress

GH comms; environment setup; issue subdivided; main issue fix attempted (PR pushed).

### Week [2] Progress

Further comms (invited to Discord); secondary issue fix attempted; both PRs proven erroneous (violated an unwritten but implied program standard).

### Week [3] Progress

Ran out of internal C:\ drive space and had to reinstall Ubuntu (via WSL) on an external spinning drive; re-establishment of dev environment; fixed original PR with new commit. Also addressed two other related sub-issues.

### Week [4] Progress

Nothing new, really. Updated this document to reflect that this PR cycle really addressed three of five sub-issues. Those three were a closely-related subset of the original parent issue.

### Code Changes

- **Files modified:** `wesnoth/data/core/team-colors.cfg` `wesnoth/src/team.cpp`
- **Key commits:** [cd25696488b22d57aa9cf6dde1f5afd3281b1112](https://github.com/wesnoth/wesnoth/pull/11282/changes/cd25696488b22d57aa9cf6dde1f5afd3281b1112); [02f8437060aae06d7f6e691cda28a47786436422](https://github.com/wesnoth/wesnoth/pull/11289/changes/02f8437060aae06d7f6e691cda28a47786436422); [bf04a83d7fe461ee4d6ef7b62d65dbbebd493297](https://github.com/wesnoth/wesnoth/pull/11294/changes/bf04a83d7fe461ee4d6ef7b62d65dbbebd493297)
- **Approach decisions:** Reordering `[color_change]` blocks is the only safe way to append the list of available colors. Enabling in place impacts order of original colors, which is undesirable. Changing fallback color must occur downstream from the color expansion and is most safely accomplished by amending existing relevant logic. Stratifying reds so they're more distinguishable is as easy as changing rgb hex values, but requires some eyeballing of hues.

---

## Pull Request

**PR Link:** [#11282](https://github.com/wesnoth/wesnoth/pull/11282); [#11289](https://github.com/wesnoth/wesnoth/pull/11289); [#11294](https://github.com/wesnoth/wesnoth/pull/11294)

**PR Description:** Enables six additional colors in `./data/core/team-colors.cfg`; changes fallback/overflow color to one of those newly-enabled colors; and makes red variants more distinguishable from one another on the minimap. Addresses three of five distinct sub-issues of issue #4123.

**Maintainer Feedback:**
### 11282
- [20260614:](https://github.com/wesnoth/wesnoth/pull/11282#issuecomment-4703294439) Maintainer noted that a part of the issue didn't seem to be addressed.
- [20260614:](https://github.com/wesnoth/wesnoth/pull/11282#issuecomment-4703351586) I pointed out that the part of the issue noted was really a separate issue.

**Status:** [Awaiting review / Iterating / Approved / **<ins>Merged</ins>**]

### 11289
- [20260713:](https://github.com/wesnoth/wesnoth/pull/11289#discussion_r3573128840) Maintainer questioned my (really the LLM's) approach and suggested it could be simpler.
- 20260715: I agreed and pushed a relevant change.
- 20260717: Discovered maintainer's suggestion conflicts with a CI test.

**Status:** [Awaiting review / **<ins>Iterating</ins>** / Approved / Merged]

### 11294
- [20260715:](https://github.com/wesnoth/wesnoth/pull/11294#issuecomment-4982691642) Maintainer pointed out that my commit contained duplicate code.
- [20260715:](https://github.com/wesnoth/wesnoth/pull/11294#issuecomment-4984328895) I discovered that this occurred because I'd resolved a merge conflict against a master branch version of the file that had changed (due to #11282) since opening the PR. Had to rebase and just make the change again.

**Status:** [Awaiting review / Iterating / Approved / **<ins>Merged</ins>**]

---

## Learnings & Reflections

Mostly Git and version control. Lots of commit juggling.

### Technical Skills Gained

Local dev environment setup for a C++ project; more fine-grained commit management (I had to rescind and alter several) in a forked/official repo loop; some WML (proprietary to Wesnoth but it's something).

### Challenges Overcome

Digging through code to find and understand the color processing pipeline. I'm barely versed in `grep` and was using Copilot (in-browser; fed links to the official GH repo for reference) so understanding anything for the first couple days was like wading through a ball pit in drunk goggles. Now I generally get how it works and am ready to drill into it for related issues very shortly.

### What I'd Do Differently Next Time

First, just learn how to search for things in a codebase properly (i.e. use `grep` intelligently). Second, just use AI in the coding tool so it can actually see the codebase and access whatever better models are available (I think Copilot does this in VS Code). I'd also slow down a bit and try to determine what is and isn't a deliberate design decision in whatever I'm modifying -- I got caught off-guard by the ordering of the original nine thing. 

---

## Resources Used

- [Wesnoth Wiki: WML](https://wiki.wesnoth.org/GameConfigWML)
- [Wesnoth Wiki: Terrain](https://wiki.wesnoth.org/TerrainWML)
- My initial (slightly eager) [attempt](https://github.com/wesnoth/wesnoth/pull/11289) at addressing [#11283](https://github.com/wesnoth/wesnoth/issues/11283)
