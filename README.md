NOTE: THIS IS A WORK-IN-PROGRESS

# RPC Syntax Documentation

The Risky Plays Calculator (RPC) accepts a string of comma-delimited commands that represent actions that require dice rolls.

> **Example input:** `2+, 6+r1, 2b(4)`

## D6 rolls
Normal rolls on a d6 are the simplest, so they have the simplest syntax: `n+,` where `n` is the target number.

> **Example:** For a 4+ dodge followed by a 3+ dodge, the syntax is `2+, 3+`.

### Skill re-rolls
For d6 rolls where the player has a skill that lets you re-roll the die, append `r` to the command.

> **Example:** An Amazon linewoman with the Dodge skill needs to make 3+ dodge. The syntax is `3+r`.

### Same skill re-rolls
Per default, the interpretor assumes that an unmarked re-roll represents a *new skill* used by a *new player*.

If it's the *same player* using the *same skill* more than once, you need to create a *skill group* for those actions. You do this by appending a unique number 1-9 after the `r`, like `r1`, `r2`, `r3`, etc.

> **Example** An Amazon linewoman needs to make two consecutive 3+ dodges. The syntax is `3+r1, 3+r1`.

> **Example:** An Amazon linewoman needs to make a 4+ dodge followed by a 3+ dodge. The syntax is `4+r1, 3+r1`.

## "Safe" rolls
Per default, the interpreter assumes that a failed d6 roll will result in a turnover. If this isn't the case, add the argument `(s)` to your input.

>**Example:** A dwarf slayer needs a 4+ Dauntless roll to get two block dice, but a failure will not result in a turnover. The syntax is `4+(s)`

# Blocks
The syntax for a block action consists of the number of block dice to roll, the keyword `b`, and then the number of acceptable outcomes, like `2b4`.

**Example:** A Saurus makes a two-die block. The acceptable outcomes are Defender Down, Defender Stumbles and Push. The syntax is `2b4`.

## Acceptable outcomes
To determine the number of acceptable outcomes, add up the values from this chart:

|-|-|
| Defender Down ("Pow"): | 1 |
| Defender Stumbles | 1 |
| Both Down | 1 |
| Push | 2 |

**Example:** A Saurus has a 2-die block without the Block skill, so you only want a result of Defender Stumbles, Defender Down or Push. The syntax is `2b4`.

## Blocks and turnovers
The interpreter assumes that only a result of Attacker Down ("Skulls") will result in a turnover. Don't include a result of "Both Down" as acceptable unless the blocking player has the Block skill.

*A note on Block:* Don't automatically include Both Down as acceptable if the player has the Block skill either. If the target has Block too, this might not be an acceptable outcome.

## Negative dice blocks
Blocks where the opponent chooses the result take a minus `-` before the number of dice, like `-2b(4)`.

**Example:** A Human Lineman (S3) blocks a Saurus (S4) without any assists. He wants a Push to set up a crowdsurf. The syntax for this is `-2b(2)`.
