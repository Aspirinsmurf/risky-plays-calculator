// THIS IS A WORK-IN-PROGRESS

# Syntax Reference

## Introduction
RiskyPlay is a probability calculator and risk analysis tool for Blood Bowl 2020. It's like AnyDice, but for Blood Bowl. And like AnyDice, it uses a human-readable notation to enable rapid input of sequences of actions.

The input is text, and the output is one or more tables of action sequences and probabilities.

## The Basics
At it's most basic, input consists of a series of comma-delimited commands that represent a sequence of actions by a player, terminated with a semicolon.

An example input could look like this:
```
dodge 4+, sure hands 2+, 2+, 2d block(dn, ds, p);
```
> In this example, one player tries dodging out with the Dodge skill on a 4+, picking up the ball with Sure Hands on a 2+, making a 2+ roll rush, blitzing with two block dice and getting either defender down, defender stumbles or a push.

### Multiple players
To input actions from multiple players, replace the comma separator with a semicolon for every new player:
```
dodge 4+, sure hands 2+, 2+, 2 block(dn,ds,p);
dodge 3+, dodge 3+;
4+, sure feet 2+, sure feet 2+;
4+, sure feet 2+, sure feet 2+;
```
> In this example, the actions on each line are done by four different players. The first player tries to perform the same sequence as before. Then the second player attempts two consecutive 3+ dodges with the Dodge skill. The third player then attempts a 4+ dodge without the Dodge skill and two rushes with Sure Feet. Finally, the fourth player attempts the same actions as the third.

### Short form
Keywords have short-form alternatives, and you can generate multiple identical actions or action sequences with multiplication syntax. An advanced user might want to write the code from the previous example much more succinctly, like this:
```
d4, h2, 2, 2b4;
2*d3;
2*(4, 2*f2;)
```
or even in a single line, like this:
```
d4,h2,2,2b4;2*d3;2*(4,2*f2;)
```
You can mix and match the short and long forms however you want.

### Flags
A flag is a virtual action added to a player's action sequence to account for unusual circumstances, such as having an usual skill.

> **Example:** A loner player (without the Dodge skill) trying to make a 4+ dodge and a 2+ dodge. This could be represented with a flag as follows:
```
loner, 4+, 3+;
```
A flag is valid for all subsequent actions in that player's action sequence. So for the example above, using `4+, 3+, loner;` would give the wrong results!

Multiple flags can be added to a player's action sequence.

For example, if the Loner player from the previous example was also a Pro, this could be represented by:
```
loner, pro, 4+, 3+;
```

## Argument
An argument is additional information appended to the end of an action expression to account for unusual circumstances. They use the syntax `!keyword`, sometimes with an additional variable for degree or effect.

For example, if a player's dodge attempt could be affected by an opposing player's Diving Tackle skill, you could write:
```
dodge 4+ !diving tackle;
```
An action can have multiple arguments.

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
Everything after the `output:` will be added to a separate, independent output table. This lets you easily compare different lines of play.

Optionally, you can label the different tables with `output "label":` or `o "label":`.

### Miscellaneous
The interpreter ignores whitespace and newlines. It's case-insensitive, so `SUREFEET` and `sure feet` are identical. Labels inside "double quotes" preserve case and spaces, but they're not used in calculations.

## Actions
RiskyPlay supports many types of dice rolls, such as normal d6 rolls, skill rolls, blocks, armor breaks, injury rolls, and arbitrary rolls.

###  Normal rolls
The syntax for a normal d6 roll is `n+`, where `n` is the target number.

Use this syntax when the player doesn't have a skill re-roll for the attempted action.

> **Example:** A Human Lineman without the Dodge skill attempts a 4+ dodge. The syntax is ``4+``.

The plus sign `+` is optional and can be omitted.

Optionally, you can add a label to a normal roll with `"label"` before it, e.g. `"Rush" 2+` or `"Dodge" 4+`. The label will be printed to the action's line in the output table. This can be useful for clarity, but it won't affect the calculation.

### Skill rolls
There are five re-roll skills in the game: Dodge, Pass, Catch, Sure Hands, and Sure Feet.

The syntax for a 2+ roll with each skill in sequence would be:
```
dodge 2+, pass 2+, catch 2+, sure hands 2+, sure feet 2+
```
Or in short form Dodge: `d2`, Pass: `p2`, Catch: `c2`, Sure Hands: `sh2` and Sure Feet: `sf2`. 

> **Example:** An Amazon linewoman with the Dodge attempts a 4+ dodge. The syntax is `dodge 4+` or just `d4`.

Sure Hands and Sure Feet can be further shortened to `h` and `f` respectively.

## Blocks

A block action consists of the number of block dice to roll, the keyword `block` and the acceptable outcomes, like `2block(dn,ds,p)`.

> **Example:** A Saurus makes a two-die block. The acceptable outcomes are Defender Down, Defender Stumbles and Push. The syntax is `2block(dd,ds,p)`.

The keyword `block` can be shortened to `b`, giving `2b(dd,ds,p)` as the short form for the example above.

If there are no digits before the keyword, the parser defaults to a 1-die block. The inputs `b4` and `1b` are synonymous.

Note that there's no separate Blitz action.

###  Acceptable outcomes
To determine the acceptable outcomes, consult this chart.

|Result            |Syntax|Value|
|------------------|------|-----|
|Defender Down     |`dd`  |1    |
|Defender Stumbles |`ds`  |1    |
|Both Down         |`bd`  |1    |
|Push              |`p`   |2    |

You can either list the acceptable results in the parantheses like `(dd,ds,bd)`, or you can add up their values and use the total value `n` (with or without parentheses). Defender Down also has the synonym `pow`.

> **Example:** A Saurus has a 2-die block against a Dark Elf Lineman. The only acceptable result is Defender Down, which has a value of one. The syntax is `2block1` or `2b1`.

// REQUEST FOR COMMENT: Is this the most intuitive way to deal with acceptable outcomes from a block? Or should it rather be in the format of 1+, 2+, etc.? I often think of block results in terms of the latter, but I rarely use actual Block Dice in play, so I'm biased.

**Note:** The interpreter assumes that only a result of Attacker Down will result in a turnover. Don't include a result of "Both Down" as acceptable unless the blocking player has Block (or Wrestle and is fine with using it).

###  Negative dice blocks
For blocks where the opponent chooses the result, add a minus sign `-` before the number of block dice, like `-2block` or `-2b`.

> **Example:** A Human Lineman (S3) blocks a Saurus (S4) without any assists. He wants a Push to set up a crowd-surf. The syntax is `-2block2` or `-2b4`.

## Armor Breaks
The calculator treats Armor Breaks as an independent action. The syntax for an Armor Break is `AV n+`, where ``n`` is the target number. The keyword `AV` can be shortened to `a` and the plus sign `+` omitted. 

> **Example:** A Saurus has AV 9+, so the syntax for the armor break is `AV 9+` or `a9` for short.

The parser assumes that armor breaks are "safe" rolls and cannot result in a turnover.

**Note:** Don't chain an Armor Break after a block that accepts Push results. You will get the wrong results.

## Foul Action
Fouling works exactly like Armor Breaks, except you use the keyword `foul`. This can be shortened to `f`.

The parser assumes that a result of double 1's will result in a turnover, and that rolls below the target will result in a failure.

## Injury Rolls
The syntax for an Injury Roll is `injury n+`, where `n` is the target number. The keyword `injury` can be shortened to `i`.

> **Example:** You want an annoying player with Stunty off the pitch, so you need a K.O. or better. Consulting the injury table, you see that you need to roll 7+. The input is `injury 7+` or `i7` for short.

The parser assumes that injury rolls are "safe" and will not result in a turnover on a failure.


### Multiplication
You can group a number of identical actions by the same player into one command by multiplication:
```
2*d3, 3*2;
```
> This example could be two 3+ dodges with the Dodge skill, followed by three 2+ rushes by the same player (with Sprint).

You can use the letter `x` instead of `*` if you prefer.

### With multiple players
If you want to repeat an identical series of actions with different (but functionally identical) players, you need to enclose the entire activation in parentheses, including the semicolon.
```
2*(d3, 3*2;)
```
> This example could be one 3+ dodge with the Dodge skill, followed by three 2+ rushes by the same player (with Sprint). Then a different player attempts to do exactly the same thing.

This isn't always an important distinction, but it's important if they have skill re-rolls.

> **Without parentheses:** You want to know the odds of all three of your rookie Human Linemen making their 4+ dodges to get away from the Line of Scrimmage. You could use `3*d4+;` for this.

> **With parentheses:** Same scenario, but the players all have the Dodge skill. Using `3*d4` will now give the wrong results. You will need to use `3*(d4+;)`;

You can multiply complex action sequences like this too.

> **Example:** You could use `3*(4+, 2*2+;)` for three different players each trying to make a 4+ dodge and two rushes.

## Other Skills

### Dauntless `DL`
Dauntless can be added as an argument or a flag with the syntax `dauntless n+`, where `n` is the target number required to get one extra die on the block. The short form is `dl`, and you can omit the plus sign `+`.

The Dauntless rule will be calculated for all subsequent blocks in the action sequence.

If the active player also has Multiblock, you may need to correct the value of `n` by adding more `dauntless` flags to the sequence at the correct position.

You can also pass Dauntless in as an argument to a Block Action, e.g.:
```
1b4!dl;
```

### Dirty Player (x)
Dirty Player can be added as a flag with syntax `dirty player x`, where `x` is the value of the skill. The short form is `dp`. If omitted, `x` defaults to `1`. 

Adding Dirty Player as a flag will affect the next Foul and Injury Roll in that player's action sequence.

Dirty Player can also be added as an argument to Foul Actions and Injury Rolls, but that's usually just more verbose.

// REQUEST FOR COMMENT: Can you do a Blitz+Foul?

### Diving Tackle
The effects of an opposing Diving Tackle can be added either as a flag or as an argument to a normal roll or a dodge skill roll.

The keyword is `diving tackle` in both cases. This can be shortened to `dt`, or even ".

For example, let's say your player attempts a 3+ dodge away from an opposing player with Diving Tackle. You can use the syntax: `dt, 3+;`

If multiple dodge attempts would be affected by Diving Tackle, you might want to use e.g. `dt, 3+, 3+;` instead of `3+!dt, 3+!dt, 3+!dt;.

If only the last dodge attempt would be affected by Diving Tackle, you could use e.g. `3+, dt, 3+;` or `3+, 3+!dt;`.

If only the first dodge attempt would be affected by Diving Tackle, you need to pass the argument, e.g. `3+!dt, 3+;`.

You don't have to adjust the target number yourself. The parser assumes that the opponent will use the skill optimally.

**Note:** If you have no team re-rolls and no other source of a re-roll for the dodge attempt, you can ignore Diving Tackle and instead adjust the target number by +2. 
