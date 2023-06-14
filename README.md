* * *
*// THIS IS VERY MUCH A WORK-IN-PROGRESS*
* * *

# Syntax Reference

## Introduction
RiskyPlay is a probability calculator and risk analysis tool for Blood Bowl 2020. It uses a human-readable notation to enable rapid input of sequences of actions.

The input is text, and the output is one or more tables of action sequences and probabilities.

## The Basics
At it's most basic, input consists of a series of comma-delimited commands that represent a sequence of actions by a player, terminated with a semicolon.

An example input could look like this:
```
dodge 4+, sure hands 2+, 2+, 2block4;
```
> In this example, one player tries dodging out with the Dodge skill on a 4+, picking up the ball with Sure Hands on a 2+, making a 2+ roll rush, blitzing with two block dice and getting either defender down, defender stumbles or a push.

* * *
*REQUEST FOR COMMENT: Should the terminology "action sequence" be ditched in favor of "action chain"?*
* * *
The output would be:

|#|Description|
|-|-|
|1|Dodge 4+ w/dodge|
|2|Pickup 2+ w/sure hands|
|2|Unskilled 2+|
|2|2-die block (4/6)|

### Multiple players
To input actions from multiple players, replace the comma separator with a semicolon for every new player activated after the first:
```
dodge 4+, sure hands 2+, 2+, 2block4;
dodge 3+, dodge 3+;
4+, sure feet 2+, sure feet 2+;
4+, sure feet 2+, sure feet 2+;
```
> In this example, the actions on each line are done by four different players. The first player tries to perform the same sequence as before. Then the second player attempts two consecutive 3+ dodges with the Dodge skill. The third player then attempts a 4+ dodge without the Dodge skill and two rushes with Sure Feet. Finally, the fourth player attempts the same actions as the third.

### Short form
Keywords have short-form alternatives, and you can generate multiple identical actions or action sequences with multiplication syntax.

An advanced user might want to write the code from the previous example much more succinctly, like this:
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

Optionally, you can label the different tables with `output: "label"` or `o:"label"`.

### Flags
A flag is a virtual action added to a player's action sequence to account for unusual circumstances, such as having an usual skill or special rule.

> **Example:** A loner player (without the Dodge skill) attempts to make a 4+ dodge and a 3+ dodge. This could be represented with a flag as follows:
```
loner, 4+, 3+;
```
A flag is valid for all subsequent actions in that player's action sequence. So for the example above, using `4+, 3+, loner;` would give the wrong results!

Multiple flags can be added to a player's action sequence.

For example, if the Loner player from the previous example was also a Pro, this could be represented by:
```
loner, pro, 4+, 3+;
```
* * *
*// REQUEST FOR COMMENT: Should there be a way to markup flags as different from actions, e.g. `#keyword?` Should this markup be obligatory?*
* * *
## Arguments
An argument is additional information appended to the end of an action expression to account for unusual circumstances. They use the syntax `!keyword`, sometimes with an additional variable for degree or effect.

For example, if a player's dodge attempt could be affected by an opposing player's Diving Tackle skill, you could write:
```
dodge 4+ !diving tackle;
```
An action can have a whole list of different arguments, like `action !arg1 !arg2`
* * *
*// REQUEST FOR COMMENT: I'm not overly fond of having both flags and arguments, but they each have subtly different use cases. I'm willing to discuss alternatives if some genius has better ideas.*
* * *

### Miscellaneous
The interpreter ignores whitespace and newlines. It's case-insensitive, so `SUREFEET` and `sure feet` are identical. Labels inside "double quotes" preserve case and spaces, but they're not used in calculations.

## Actions
RiskyPlay supports many types of dice rolls, such as normal d6 rolls, skill rolls, blocks, armor breaks, injury rolls, and arbitrary rolls.

###  Normal rolls
The syntax for a normal d6 roll is `n+`, where `n` is the target number.

Use this syntax when the player doesn't have a skill re-roll for the attempted action.

> **Example:** A Human Lineman without the Dodge skill attempts a 4+ dodge. The syntax is ``4+``.

The plus sign `+` is always optional.

Optionally, you can add a label to a normal roll with `"label"` before it, e.g. `"Rush" 2+` or `"Dodge" 4+`. The label will be printed to the action's line in the output table. This can be useful for clarity, but it won't affect the calculation.

### Skill rolls
There are five reroll skills in the game: Dodge, Pass, Catch, Sure Hands, and Sure Feet.

The syntax for a 2+ roll with each skill in sequence would be:
```
dodge 2+, pass 2+, catch 2+, sure hands 2+, sure feet 2+
```
Or in short form Dodge: `d2`, Pass: `p2`, Catch: `c2`, Sure Hands: `sh2` and Sure Feet: `sf2`. 

> **Example:** An Amazon linewoman with the Dodge attempts a 4+ dodge. The syntax is `dodge 4+` or just `d4`.

Sure Hands and Sure Feet can be further shortened to `h` and `f` respectively.

Alternatively, you can add the skill's keyword as an argument to a normal roll. The syntax `2+ !sf` is equal to `sf2+`.

## Blocks

A block action consists of the number of block dice to roll, the keyword `block` and the number of acceptable outcomes, like `2block4`.

> **Example:** A Saurus makes a two-die block. The acceptable outcomes are Defender Down, Defender Stumbles and Push. The syntax is `2block4`.

The keyword `block` can be shortened to `b`, giving `2b4` as the short form for the example above.

If there are no digits before the keyword, the parser defaults to a 1-die block. The inputs `b4` and `1b4` are synonymous.

Note that there's no separate Blitz action.

###  Acceptable outcomes
To determine the number of acceptable outcomes, add up the values of the desired results from this chart.

|Result            |Value|
|------------------|-----|
|Defender Down     |1    |
|Defender Stumbles |1    |
|Both Down         |1    |
|Push              |2    |

> **Example:** A Saurus has a 2-die block against a Dark Elf Lineman. The only acceptable result is Defender Down, which has a value of one. The syntax is `2block1` or `2b1`.

* * *
*// REQUEST FOR COMMENT: Is this the most intuitive way to deal with acceptable outcomes from a block? Or should it rather be in the format of 1+, 2+, etc.? I often think of block die results in terms of the latter, but I rarely use actual Block Dice in play, so I might be biased.*

*Also, should people be able to enter the desired results precisely, like `1b(DD, DS, BD)`? I don't really see the point in allowing that, but I **can** see scenarios where counting up the values could actually be slower than just typing it in for new users.*
* * *
*REQUEST FOR COMMENTS: There's an elephant in the room here.... To calculate the correct T/O chance, we kinda need to know if the player has Block/Wrestle or not. And the calculator should ideally track the T/O chance separately from the success and failure chance. A "failure" should be whatever the player deems inadequate in order to succeed, not necessarily what will lead to a turnover.*

*Both `FailureChance > TurnoverChance` and `TurnoverChance < FailureChance` should in theory be possible states, with `FailureChance` defined as `1 - SuccessChance`.*

*But for example `2b3` can mean either (DD, DS, BD) or (DD, P, P). The success chance is equal, and so is the failure chance. Yet in the first case, a player presumably has a 1/36 chance of a T/O, with just P(AD) as critical failure state. In the second case, it's presumably 2/36 with P(AD||BD) as critical failure states. Or it might not be, since the player **could** have block/tackle even if the coach considers a BD result to be unacceptable (doesn't move target without a K.O. or injury).*

*So the question becomes: how to solve it? A good solution might be `bb` as short-hand for a block **with** the Block and/or Wrestle wrestle skill. But what should be the long form? Maybe arguments like `2block3 !block` and/or `2block3 !wrestle`?*

*That's awfully verbose, but I suppose most people should be able to grok the short-hand anyway. I'd like to hear better suggestions though. An empty argument like `2block3!` perhaps? The format `2b3!` could work for short too, but I think I prefer `2bb3` to that option.*
* * *

###  Negative dice blocks
For blocks where the opponent chooses the result, add a minus sign `-` before the number of block dice, like `-2block` or `-2b`.

> **Example:** A Human Lineman (S3) blocks a Saurus (S4) without any assists. He wants a Push to set up a crowd-surf. The syntax is `-2block2` or `-2b2`.

## Armor Breaks
The syntax for an Armor Break is `AV n+`, where ``n`` is the target number. The keyword `AV` can be shortened to `a`.

> **Example:** A Saurus has AV 9+, so the syntax for the armor break is `AV 9+` or `a9` for short.

The parser assumes that armor breaks are "safe" rolls that cannot result in a turnover.

**Note:** Don't chain an Armor Break after a block that accepts Push results. You will get the wrong results.

## Foul Action
Fouling works exactly like Armor Breaks, except you use the keyword `foul`, with no short form.

The parser assumes that a result of double 1's will result in a turnover, and that rolls below the target number will result in a failure.

## Injury Rolls
The syntax for an Injury Roll is `injury n+`, where `n` is the target number. The keyword `injury` can be shortened to `i`.

> **Example:** You want an annoying player with Stunty off the pitch, so you need a K.O. or better. Consulting the injury table, you see that you need to roll 7+. The input is `injury 7+` or `i7` for short.

The parser assumes that injury rolls are "safe" and will not result in a turnover on a failure. 

### Fouls & Injuries
If an Injury Roll was preceded by a Foul Action at any point prior in the player's action sequence, the parser will assume that a result of double 1's will result in a turnover and that rolls below the target number will result in a failure.


### Pass Interference
* * *
*// Idk... maybe something like `1–(n+)`, with `n` being the opponent's chance to deflect? That makes sense if you understand the math behind it (opposite event rule!), but maybe not otherwise. Perhaps it should instead be `(n+)` as input, in a vague parallel to how they sometimes use parantheses for negatives in finance?*

*// You could also use some arbitrary odds `n/6`, if you care to count it out. But that seems a lot less user-friendly to me.*

*// The same idea applies to e.g. Shadowing. If your line of play requires that an opponent doesn't suceed on Shadowing, it should be possible to denote that in an intuitive fashion. A general way to take any expression and turning it into `1-<expr>` would be useful.*
* * *

### Arbitrary Odds
* * *
*// Probably `n / d`, with `n` as the numerator and `d` as the denominator. Optionally with an argument to make it safe against Turnovers, or maybe that should be default behavior. It's mostly for bounce results, yeah? In theory you can analyze weather and kick-off events too, but that seems **very** niche.*

*// Scatter and bounce could get spicy with logical OR as a possible operator, see comments under Frenzy. If you could input something like "I either catch it or it bounces to one of these squares", the utility goes way up for e.g. analyzing really hairy ballsacks.*
* * *

## Advanced Features

### Multiplication
You can group a number of identical actions by the same player into one command by multiplication:
```
2*4+, 3*2;
```
> This example could be two 4+ dodges, followed by three 2+ Rushes by the same player (with Sprint).

You can use the letter `x` instead of `*` if you prefer.

### With multiple players
If you want to repeat an identical series of actions with different (but functionally identical) players, you need to enclose the entire activation in parentheses, including the semicolon.
```
2*(4+, 2*2+;)
```
> This example could be one 3+ dodge with the Dodge skill, followed by two 2+ rushes by the same player. Then, a different player attempts exactly the same sequence of actions.

## Other Skills

### Dauntless
Dauntless can be added as an argument or a flag with the syntax `dauntless n+`, where `n` is the target number required to get one extra die on the block. The short form is `dl`.

The Dauntless rule will be calculated for all subsequent blocks in the action sequence.

If the active player also has Multiblock, you may need to correct the value of `n` by adding more `dauntless` flags to the sequence at the correct position.

You can also pass Dauntless in as an argument to a Block Action, e.g.: `1b4!dauntless4;` or `1b4!dl4`.

### Dirty Player (x)
Dirty Player can be added as a flag with syntax `dirty player x`, where `x` is the value of the skill. The short form is `dp`. If omitted, `x` defaults to `1`. 

Adding Dirty Player as a flag will affect the next Foul and Injury Roll in that player's action sequence.

Dirty Player can also be added as an argument to Foul Actions and Injury Rolls, but that's usually just more verbose.

*// REQUEST FOR COMMENT: Can you do a Blitz+Foul? Foul+Blitz?*

### Diving Tackle
The effects of an opposing Diving Tackle can be added either as a flag or as an argument to a normal roll or a dodge skill roll.

The keyword is `diving tackle` in both cases. This can be shortened to `dt`.

For example, if your player attempts a 3+ dodge away from an opposing player with Diving Tackle, you can use either `dt, 3+;` or `3+!dt`.

If multiple dodge attempts would be affected by Diving Tackle, you might want to use e.g. `dt, 3+, 3+;` instead of `3+!dt, 3+!dt, 3+!dt`;.

If only the last dodge attempt would be affected by Diving Tackle, you could use e.g. `3+, dt, 3+;` or `3+, 3+!dt;`.

If only the first dodge attempt would be affected by Diving Tackle, you will need to pass in the argument, like `3+!dt, 3+;`.

The parser assumes that the opponent will use the skill optimally in a generic sense (force a reroll if possible). But since there's a chance you might be forced to reroll *without* the use of Diving Tackle anyway, the opponent will add it to the *final roll* that percentage of the time.
* * *
*// The math there gets complex, but I think it's doable. It's what Dave's calculator does, I think.*

**Note:** If you have no team re-rolls and no other source of a re-roll for the dodge attempt, you can ignore Diving Tackle and instead adjust the target number by +2 manually.

## Jump Up
There's no special syntax for Jump Up. You can simulate its effect using a normal action and a block. The parser doesn't enforce the number of "blitzes" you can have in a turn.

Example: Dave the Orc wants to Jump Up and punch an orc in the face, hoping for Defender Down on a two-die block. Use a syntax like `5+, 2b1;`.

## Frenzy
* * *
*// this entire section is very much undecided and WIP*

Properly representing Frenzy requires some sort of control flow (with logical OR operator), since the math suddenly requires addition and becomes hellishly convoluted with re-rolls.

Let's say you want to know the odds of getting at least a K.O. on a target with AV 7+ on your two one-die blocks from a player with Frenzy.

This result can happen in one of two mutually exclusive ways. You can either 1) succeed on the first block (DD or DS), or 2) get a Push on the first block and then succeed on the second block.

The first outcome can be notated `1b2`. The second outcome can be notated `1b2, 1b1`.

To represent the odds of either one *or* the other being the desired outcome, we need to use some kind of syntax resembling a logical OR operator. Here's a possible solution:
```
(1b1 | 1b2, 1b1);
```

This could parse into (1/6) + (2/36) * (1/6) for the chance of success.

Then you can add the armor break and the injury roll at the end, like this:
```
(1b1 | 1b2, 1b2), AV 7+, injury 8+;
```

Now, if the action is a Blitz and the Frenzy player needs to make a Rush in order to make the second Block, you will need to add that expression to the right-hand side of the operator as well.

```
(1b1 | 1b2, 2+, 1b2), a7, i8;
```

If the blitz requires *two* rushes, one before and one after the block, the result would be:
```
2+, (1b1 | 1b2, 2+, 1b2), a7, i8;
```

...and that's quite a mouthful!

But if you don't care about the injury and armor breaks, you can probably simplify this to `1b1; | 2b2, 1b2;` and `1b1; | 2b2, 2+, 1b2;` respectively. The parentheses might not be needed there, `but 2+, (1b1 | 1b2, 2+, 1b2)` would still require them. 

But it gets worse... if the defending player has Dodge and you don't have Tackle, the chance of getting a push is now `b3` instead of `b2`. This something you just need to remember, I guess?
* * *
*// REQUEST FOR COMMENTS
Is it possible to come up with some unique syntax that will cover the most common case here? Maybe have a keyword `frenzy` that takes the block dice on the left side of the operator and recreates the "push,push" chance programmatically on the right? The next term could perhaps also be programmatically generated, unless the player knows they're in a Frenzy Trap or Frenzy Assist scenario.*

*// Interactions with Side Step are also really problematic, but I think we'll just have to assume that the user will know where an opponent might want to bait him on a push.*

*// I guess SAMBA and Dave's calculator just don't even bother with Frenzy, which is a shame.*
* * *


## Special Rules

## Argue the Call
You can add `argue the call` or `atc` as a flag to a player's action sequence. It should properly effect all subsequent Foul actions and Injury Rolls in that player's action sequence.

## Bribe
You can add `bribe` as a flag to a player's actions sequence. It should properly effect all subsequent Foul actions and Injury Rolls in that player's action sequence.
* * *
*// How do multiple bribes work? If I get snake-eyes twice on a foul, can I use two bribes?*
* * *

