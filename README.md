# Syntax Reference

## Introduction
RiskyPlay is a probability calculator and analysis tool for Blood Bowl 2020. It uses a human-readable language to enable swift input of complex sequences of actions.

## The Basics

Input consists of a series of comma-delimited commands that represent a sequence of actions.

An example input could look like this:
```
dodge 4+, sure hands 2+, 2+, 2block4
```
> *In this example, one player would be dodging out with the Dodge skill, picking up the ball with Sure Hands, making 2+ roll on a rush and blitzing with two dice and getting either defender down, defender stumbles or a push.*

To add actions from additional players, replace the comma separator between them with a semicolon:
```
dodge 4+, sure hands 2+, 2+, 2block;
dodge 4+, dodge 4+;
dodge 4+, sure feet 2+, sure feet 2+
```
In this example, the actions on each line are done by different players.

The interpreter ignores whitespace and line breaks. In addition, there are short-form synonyms for every action. So the same code could be succinctly rewritten as:

```
d4,h2,f2,2b4;4;d4,f2,f2
```

If you want to add to an existing entries table with actions from a new player, start the string with a semicolon:
```
;dodge 4+, dodge 4+
```

# The Dice Rolls
RiskyPlay supports six different types of dice rolls: normal d6 rolls, skill rolls, blocks, armor breaks, injury rolls and arbitrary odds. 

##  1. <a name='Normalrolls'></a>Normal rolls

The syntax for a normal d6 roll is `n+`, where `n+` is the target number.

**Example:** A Human Lineman attempts a 4+ dodge. The syntax is ``4+``.

You use this syntax when the player doesn't have a skill re-roll for that action.

The plus sign `+` is optional and can be omitted.

# Skill rolls
There are five re-roll skills in the game: dodge, pass, catch, sure hands, and sure feet.

The syntax for a 2+ roll with each skill is sequence would be:

**Long-form**

```json
dodge 2+, pass 2+, catch 2+, sure hands 2+, sure feet 2+
```

**Short-form
```json
d2, p2, c2, sh2, sf2
```

Sure hands and sure feet can be further shortened to `h2` and `f2` respectively.

> **Example:** An Amazon linewoman with the Dodge skill needs to make 3+ dodge. In short form, the syntax is `d4`.

# Blocks
A block action command consists of the number of block dice to roll and then the keyword `block`, followed by the number of acceptable outcomes, like `2block 4`.

> **Example:** A Saurus makes a two-die block. The acceptable outcomes are Defender Down, Defender Stumbles and Push. The syntax is `2block 4`.

The keyword `block` can be shortened to `b`, giving `2b4` as the short form for the example above.  

##  2. <a name='Acceptableoutcomes'></a>Acceptable outcomes
To determine the number of acceptable outcomes, add up the values from this chart:

|Result|Value|
|-|-|
|Defender Down | 1 |
|Defender Stumbles | 1 |
|Both Down | 1 |
|Push | 2 |

> **Example:** A Saurus has a 2-die block against a Dark Elf Lineman. The only acceptable result is Defender Down. The syntax is `2b4`.

##  3. <a name='Negativediceblocks'></a>Negative dice blocks
For blocks where the opponent chooses the result, add a minus sign `-` before the number of block dice, like `-2b4`.

> **Example:** A Human Lineman (S3) blocks a Saurus (S4) without any assists. He wants a Push to set up a crowdsurf. The syntax is `-2b2`.

**Turnovers**: The interpreter assumes that only a result of Attacker Down will result in a turnover. Don't include a result of "Both Down" as acceptable unless the blocking player has the Block skill.







1. Block Dice


1. New Player

##  4. <a name='Introduction'></a>1. Introduction

##  5. <a name='TheBasics'></a>1. The Basics

