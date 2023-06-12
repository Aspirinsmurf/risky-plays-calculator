
# Introduction

RiskyPlay input consists of a series of comma-delimited commands that represent actions, for example:
```
d4, h2, f2, 2b4
```

To add actions from more players, replace the comma separator with a semicolon.
```csv
d4, h2, f2, 2b4;
4, 4;
d4, f2, f2;
```
> This example would be interpreted as actions from three different players.

The interpreter ignores whitespace and line breaks, so the code above could be rewritten as:

```
d4,h2,f2,2b4;4;d4,f2,f2
```

# Normal rolls

The syntax for a normal d6 roll is *n*, where *n* is the target number.

**Example:** A human lineman attempts a 4+ dodge. The syntax is ``4``.

Use this syntax when the player doesn't have a skill re-roll for that action.

# Skill Rolls
There are five re-roll skills in the game: dodge, pass, catch, sure hands, and sure feet.

The syntax is:
```
d4, p4, c4, 4f, h4
```

> **Example:** An Amazon linewoman with the Dodge skill needs to make 3+ dodge. The syntax is `d4`.

# Blocks
A block action consists of the number of block dice, the keyword `b`, and then the number of acceptable outcomes, like `2b4`.

> **Example:** A Saurus makes a two-die block. The acceptable outcomes are Defender Down, Defender Stumbles and Push. The syntax is `2b4`.

## Acceptable outcomes
To determine the number of acceptable outcomes, add up the desired values from this chart:

|Result|Value|
|-|-|
|Defender Down ("Pow"): | 1 |
|Defender Stumbles | 1 |
|Both Down | 1 |
|Push | 2 |

> **Example:** A Saurus has a 2-die block. He wants a result of Defender Stumbles, Defender Down or Push. The syntax is `2b4`.

## Negative dice blocks
For blocks where the opponent chooses the result, add a minus sign `-` before the number of dice, like `-2b4`.

> **Example:** A Human Lineman (S3) blocks a Saurus (S4) without any assists. He wants a Push to set up a crowdsurf. The syntax is `-2b2`.

## Blocks and turnovers
The interpreter assumes that only a result of Attacker Down ("Skulls") will result in a turnover. Don't include a result of "Both Down" as acceptable unless the blocking player has the Block skill.

> *A note on Block:* Don't automatically include Both Down as acceptable if the player has the Block skill either. If the opposing player has Block too, this might not be an acceptable outcome.





1. Block Dice


1. New Player

## 1. Introduction

## 1. The Basics

