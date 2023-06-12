+ [Introduction](#introduction)
+ [The Basics](#the-basics)
+ [Dice Rolls](#dice-rolls)
    + [Normal rolls](#normal-rolls)
    + [Skill rolls](#skill-rolls)
    + [Blocks](#blocks)
    + [Armor breaks](#armor-breaks)
    + [Injury rolls](#injury-rolls)


# Introduction
RiskyPlay is a probability calculator and analysis tool for Blood Bowl 2020. It uses a human-readable language to enable swift input of complex sequences of actions.

# The Basics

Input consists of a series of comma-delimited commands that represent actions, for example:
```
d4, h2, f2, 2b4
```

To add actions from more than one player, replace the comma separator between them with a semicolon.
```
d4, h2, f2, 2b4;
4, 4;
d4, f2, f2;
```
> This example would be interpreted as actions from three different players.

The interpreter ignores whitespace and line breaks, so the code above could be rewritten as:

```
d4,h2,f2,2b4;4;d4,f2,f2
```

If you want to add to an existing table with actions from a new player, start the entire string with a semicolon:
```
;d4, h2, f2, 2b4
```

# The Dice Rolls
RiskyPlay supports six different types of dice rolls: normal d6 rolls, skill rolls, blocks, armor breaks, injury rolls and arbitrary odds. 

## Normal rolls

The syntax for a normal d6 roll is `n`, where `n` is the target number.

**Example:** A Human Lineman attempts a 4+ dodge. The syntax is ``4``.

Use this syntax when the player doesn't have a skill re-roll for that action.

# Skill rolls
There are five re-roll skills in the game: dodge, pass, catch, sure hands, and sure feet.

The syntax for a 2+ roll with each skill is:

+ **Dodge:** `d2`
+ **Pass:** `p2`
+ **Catch:** `c2`
+ **Sure hands:** `sh2`
+ **Sure feet:** `sf2`

> **Example:** An Amazon linewoman with the Dodge skill needs to make 3+ dodge. The syntax is `d4`.

# Blocks
A block action command consists of the number of block dice to roll and then the keyword `b`, followed by the number of acceptable outcomes, like `2b4`.

> **Example:** A Saurus makes a two-die block. The acceptable outcomes are Defender Down, Defender Stumbles and Push. The syntax is `2b4`.

## Acceptable outcomes
To determine the number of acceptable outcomes, add up the values from this chart:

|Result|Value|
|-|-|
|Defender Down | 1 |
|Defender Stumbles | 1 |
|Both Down | 1 |
|Push | 2 |

> **Example:** A Saurus has a 2-die block. He wants a result of Defender Stumbles, Defender Down or Push. The syntax is `2b4`.

## Negative dice blocks
For blocks where the opponent chooses the result, add a minus sign `-` before the number of block dice, like `-2b4`.

> **Example:** A Human Lineman (S3) blocks a Saurus (S4) without any assists. He wants a Push to set up a crowdsurf. The syntax is `-2b2`.

**Turnovers**: The interpreter assumes that only a result of Attacker Down will result in a turnover. Don't include a result of "Both Down" as acceptable unless the blocking player has the Block skill.


1. Block Dice


1. New Player

## 1. Introduction

## 1. The Basics

