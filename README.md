// THIS IS A WORK-IN-PROGRESS

# Syntax Reference

## Introduction
RiskyPlay is a probability calculator and risk analysis tool for Blood Bowl 2020. It uses a human-readable notation to enable rapid input of complex sequences of actions.

The input is pure text, and the output is one or more tables of action sequences and probabilities.

## The Basics
Input consists of a series of comma-delimited commands that represent a sequence of actions by a player, terminated with a semicolon.

An example input could look like this:
```
dodge 4+, sure hands 2+, "rush" 2+, 2block4;
```
> In this example, one player tries dodging out with the Dodge skill on a 4+, picking up the ball with Sure Hands on a 2+, making a 2+ roll on a Rush and Blitzing with two dice and getting either defender down, defender stumbles or a push.*

### Multiple players
To input actions from multiple players, replace the comma separator with a semicolon:
```
dodge 4+, sure hands 2+, 2+, 2block4;
dodge 3+, dodge 3+;
4+, sure feet 2+, sure feet 2+;
4+, sure feet 2+, sure feet 2+;
```
> In this example, the actions on each line are done by four different players. The first player tries to perform the same sequence as before. Then the second player attempts two consecutive 3+ dodges with the Dodge skill. The third player then attempts a 4+ dodge and two rushes with Sure Feet. Finally, the fourth player does the same as the third.

### Short-hand and grouping
There are short-form synomyms for every action, and you can group multiple identical actions together. The code from the previous example could be written much more succinctly as:
```
d4, h2, 2, 2b4;
2*d3;
2*(4, 2*f2;)
```
Or even in a single line, like this:
```
d4,h2,2,2b4;2*d3;2x(4,2*f2;)
```
The short and long forms can be mixed and matched.

### Multiple outputs
By default, the calculator outputs a single table. You can force it to give multiple outputs with the keyword `output:` or `o:`.

```
dodge 4+, sure hands 2+, 2+, 2block4;
dodge 3+, dodge 3+;
4+, sure feet 2+, sure feet 2+;

output:
4+, 2+, 2+;
dodge 3+, dodge 4+;
```
This lets you easily compare the chances of different lines of play.

### Miscellaneous
The interpreter ignores whitespace and newlines and is case-insensitive. `SUREFEET` and `sure feet` are identical.

## Dice Rolls
RiskyPlay supports many types of dice rolls, such as normal d6 rolls, skill rolls, blocks, armor breaks, injury rolls, and arbitrary rolls.

###  Normal rolls

The syntax for a normal d6 roll is `n+`, where `n+` is the target number.

Use this syntax when the player doesn't have a skill re-roll for the attempted action.

> **Example:** A Human Lineman without the Dodge skill attempts a 4+ dodge. The syntax is ``4+``.

The plus sign `+` is optional and can be omitted.

### Skill rolls
There are five re-roll skills in the game: Dodge, Pass, Catch, Sure Hands, and Sure Feet.

The syntax for a 2+ roll with each skill in sequence would be:
```
dodge 2+, pass 2+, catch 2+, sure hands 2+, sure feet 2+
```
Or in short form Dodge: `d2`, Pass: `p2`, Catch: `c2`, Sure Hands: `sh2` and Sure Feet: `sf2`. 

> **Example:** An Amazon linewoman with the Dodge skill needs to make 3+ dodge. The syntax is `dodge 4+` or just `d4`.
Sure Hands and Sure Feet can be further shortened to `h` and `f` respectively.

## Blocks

A block action consists of the number of block dice to roll, the keyword `block` and the number of acceptable outcomes, like `2block4`.

> **Example:** A Saurus makes a two-die block. The acceptable outcomes are Defender Down, Defender Stumbles and Push. The syntax is `2block4`.

The keyword `block` can be shortened to `b`, giving `2b4` as the short form for the example above.

If there are no digits before the keyword, the parser defaults to a 1-die block. The inputs `b4` and `1b4` are synonymous.

###  Acceptable outcomes
To determine the number of acceptable outcomes, consult this chart and add up the values:

|Result            |Value|
|------------------|-----|
|Defender Down     | 1   |
|Defender Stumbles | 1   |
|Both Down         | 1   |
|Push              | 2   |

> **Example:** A Saurus has a 2-die block against a Dark Elf Lineman. The only acceptable result is Defender Down. The syntax is `2block1` or `2b1`.

The interpreter assumes that only a result of Attacker Down will result in a turnover. Don't include a result of "Both Down" as acceptable unless the blocking player has the Block skill.

// REQUEST FOR COMMENT: Is this the most intuitive way to deal with acceptable outcomes from a block or should it be in the format of 1+, 2+, etc.? I often think of blocks like this, but I rarely use actual Block Dice in play, so I'm biased.

###  Negative dice blocks
For blocks where the opponent chooses the result, add a minus sign `-` before the number of block dice, like `-2block` or `-2b`.

> **Example:** A Human Lineman (S3) blocks a Saurus (S4) without any assists. He wants a Push to set up a crowd-surf. The syntax is `-2block2` or `-2b4`.

## Armor Breaks
The syntax for an armor break is `AV n+`, where ``n`` is the target number. The keyword `AV` can be shortened to `a` and the plus sign `+` omitted. 

> **Example:** A Saurus has AV 9+, so the syntax for the armor break is `AV 9+` or `a9` for short.

By default, the interpreter assumes that armor breaks are "safe" rolls and cannot result in a turnover. For Fouling and similar scenarios, see Advanced Usage.

### Injury Rolls
The syntax for an Injury roll is `injury n+`, where `n` is the target number. The keyword `injury` can be shortened to `i`.

> **Example:** You want an annoying player with Stunty off the pitch, so you need a K.O. or better. Consulting the injury table, you see that you need to roll 7+. The input is `injury 7+` or `i7` for short.

## Grouping rolls
You can group a number of identical actions by the same player into one command by multiplication:
```
2*d3, 3*2
```
> This example could be two 3+ dodges with the Dodge skill, followed by three 2+ rushes by the same player (with Sprint).

You can use the letter `x` instead of `*` if you prefer.

### With multiple players
If you want to repeat an identical series of actions with different (but functionally identical) players, you need to enclose the entire activation in parentheses, including the semicolon.

This isn't always an important distinction, but it's important if they have skill re-rolls.

> **Without parentheses:** You want to know the odds of all three of your rookie Human Linemen making their 4+ dodges to get away from the Line of Scrimmage. You could use `3*d4;`.

> **With parentheses:** Same scenario, but the players all have the Dodge skill. Using `3*d4` will now give the wrong results. You will need to use `3*(d4;)`;

You can multiply complex action sequences like this too.

> **Example:** You could use `3*(4+, 2*2;)` for three different players each trying to make a 4+ dodge and two rushes.






