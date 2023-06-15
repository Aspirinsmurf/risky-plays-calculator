* * *
*// THIS IS VERY MUCH A WORK-IN-PROGRESS*
* * *

# Syntax Specification

## Introduction
RiskyPlay is a probability calculator and risk analysis tool for Blood Bowl 2020. It uses a human-readable notation to enable rapid input of sequences of actions. The input is text, and the output is one or more tables of action sequences and probabilities.

Dave's Blood Bowl calculator already uses a complete domain-specific notation, which is specified here:
https://www.bloodbowldave.com/glossary

RiskyPlay uses a similar notation, but with a synonym-rich, human-readable grammar more suited to a text-based calculator, optimized for both comprehension and speed of input by advanced users.


### Design Goals

The input language should be:

- **Comprehensible:** Most input should be possible in a form that closely resembles English.
- **Succinct:** Most input should be possible in a form that can be typed quickly by an advanced user.
- **Flexible:** Any mixture of comprehensible and concise forms should be acceptable.
- **Creatively expressive:** There should be plenty of synonyms and different ways to express the same ideas.
- **Unsophisticated:** The most common use cases should be the easiest to express, at the cost of less common use cases if necessary.

The language does not need to be:

- **Exhaustive:** There are many possible sequences of play in Blood Bowl, but only some are interesing to analyze and calculate.
- **Consistent:** Some corner cases are properly thought of as *corner cases* (such as very unusual skill combinations), and these may well require kludgy hacks and workarounds.



## The Basics
At it's most basic, input consists of a series of comma-delimited commands that represent a sequence of actions by a player, terminated with a semicolon. Here's an example input:

    dodge 4+, sure hands 2+, 'rush' 2+, 2block4;    

> In this example, one player tries dodging out with the Dodge skill on a 4+, picking up the ball with Sure Hands on a 2+, making a 2+ roll rush, blitzing with two block dice and getting either defender down, defender stumbles or a push.

<samp>REQUEST FOR COMMENTS: Should the terminology "action sequence" be ditched in favor of "action chain" throughout?</samp>

### Additional players
To input actions from additional players, replace the comma separator with a semicolon for each new player after the first:

    dodge 4+, sure hands 2+, 2+, 2block4;
    dodge 3+, dodge 3+;
    4+, sure feet 2+, sure feet 2+;
    4+, sure feet 2+, sure feet 2+;

> In this example, the actions on each line are done by four different players. The first player tries to perform the same sequence as before. Then the second player attempts two consecutive 3+ dodges with the Dodge skill. The third player then attempts a 4+ dodge without the Dodge skill and two rushes with Sure Feet. Finally, the fourth player attempts the same actions as the third.

### Short form
Keywords have short-form synonyms, and you can generate multiple identical actions or action sequences with multiplication syntax.

An advanced user might want to write the code from the previous example much more succinctly, like this:

    d4, h2, 2, 2b4;
    2*d3;
    2*(4, 2*f2;)

This could even be in a single line, like this:

    d4,h2,2,2b4;2*d3;2*(4,2*f2);

You can mix and match the short and long forms any way you want.

### Multiple outputs
By default, the calculator outputs a single table. You can create multiple tables with the symbol `>` (or any repetition of `>`), like this:

    dodge 4+, sure hands 2+, 2+, 2block4;
    dodge 3+, dodge 3+;
    4+, sure feet 2+, sure feet 2+;
    >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
    4+, 2+, 2+;
    dodge 3+, dodge 4+;

Everything after the final `>` will be added to a separate, independent output table. This lets you easily compare different lines of play.

<samp>REQUEST FOR COMMENT: Should there be a way to label outputs?</samp>

### Flags
A flag is a virtual action added to a player's action sequence to account for unusual circumstances, such as having an usual skill or special rule.

> **Example:** A loner player (without the Dodge skill) attempts to make a 4+ dodge and a 3+ dodge. This could be represented with a flag as follows:

    loner, 4+, 3+;

A flag is valid for all subsequent actions in that player's action sequence. So for the example above, using `4+, 3+, loner;` would give the wrong results!

Multiple flags can be added to a player's action sequence. For example, if the Loner player from the previous example was also a Pro, this could be represented by:

    loner, pro, 4+, 3+;

<samp>REQUEST FOR COMMENT: Should there be a way to markup flags as different from actions, e.g. `#flag`? Should this markup be obligatory?</samp>

### Arguments
An argument is additional information appended to the end of an action expression to account for unusual circumstances. They use the syntax `!arg`, sometimes with an additional variable for degree or effect.

For example, if a player's dodge attempt could be affected by an opposing player's Diving Tackle skill, you could write:

    dodge 4+ !diving tackle;

An action can have a whole list of different arguments, like `action !arg1 !arg2`.

<samp>REQUEST FOR COMMENT: I'm not overly fond of having both flags and arguments in the language, but they each have their own subtly different use cases. I'm willing to discuss alternatives if some genius has better ideas. Also, the word "argument" is horrifying for anyone not into math and programming.</samp>

### Miscellaneous
The interpreter ignores whitespace and newlines. It's also case-insensitive, so `SUREFEET` and `sure feet` are identical. Labels inside "double quotes" or 'single quotes' preserve case and spacing, but they're never used in calculations.

## Actions
RiskyPlay supports many types of dice rolls, such as normal d6 rolls, skill rolls, blocks, armor breaks, injury rolls, and arbitrary rolls.

**Important:** The plus sign `+` is optional and can always be omitted.

###  Normal rolls
The syntax for a normal d6 roll is `n+`, where `n` is the target number.

Use this syntax when the player doesn't have a skill re-roll for the attempted action.

> **Example:** A Human Lineman without the Dodge skill attempts a 4+ dodge. The syntax is ``4+``.

**Labels:** Optionally, you can add a label to a normal roll with `'label'` before it, e.g. `'Rush' 2+` or `'Dodge' 4+`. The label will be printed to the action's line in the output table. This can be useful for clarity, but it won't affect the calculation.

Automatic labels can be prepended too, with the syntax `l:`, where `l` is a symbol from the [label chart](#label-chart).

### Skill rolls
There are five reroll skills in the game: Dodge, Pass, Catch, Sure Hands, and Sure Feet.

The syntax for a 2+ roll with each skill in sequence would be:

    dodge 2+, pass 2+, catch 2+, sure hands 2+, sure feet 2+

Or in short form Dodge: `d2`, Pass: `p2`, Catch: `c2`, Sure Hands: `sh2` and Sure Feet: `sf2`. 

> **Example:** An Amazon linewoman with Dodge attempts a 4+ dodge. The syntax is `dodge 4+` or just `d4+`.

Sure Hands and Sure Feet can be further shortened to `h` and `f` respectively.

**As arguments:** Alternatively, you can add the skill's keyword as an argument to a normal roll. The syntax `2+ !sf` is identical to `sf2+`.

### Blocks

A block action consists of the number of block dice to roll, the keyword `block` and the number of acceptable outcomes, like `2block4`.

> **Example:** A Saurus makes a two-die block. The acceptable outcomes are Defender Down, Defender Stumbles and Push. The syntax is `2block4`.

The keyword `block` can be shortened to `b`, giving `2b4` as the short form for the example above.

If there are no digits before the keyword, the it defaults to a 1-die block. The inputs `b4` and `1b4` are synonymous.

#### Block and Tackle Skills

<samp>REQUEST FOR COMMENTS: There's a bit of an elephant in the room here. To calculate the correct T/O chance, we kinda need to know if the player has Block/Wrestle or not (and if the coach is willing to use Wrestle on a BD).

<samp>Moreover, we should ideally track the T/O chance separately from the success and failure chance. A "failure" should be whatever the player deems inadequate in order to succeed, not necessarily what will lead to a turnover. Both `FailureChance > TurnoverChance` and `TurnoverChance < FailureChance` should in theory be possible states, with `FailureChance` defined with the opposition rule as `1 - SuccessChance`.

<samp>So here's the problem. A syntax like `2b3` can mean either (DD, DS, BD) or (DD, P). The success chance is equal, and so is the failure chance. But in the first case, a player presumably has a 1/36 chance of a T/O, with just P(AD) as a critical failure state. In the second case, it's presumably 2/36 with P(AD||BD) as critical failure states. Or it might not be, since the player *could* have block/tackle even if the coach considers a BD result to be unacceptable for the play to be successfull (since it doesn't *move* the target without a K.O. or injury).

<samp>So the question becomes: how to solve it? A good solution might be `bb` as short-hand for a block *with* the Block and/or Wrestle skill. But what should be the long form? Maybe arguments like `2block3 !block` and/or `2block3 !wrestle`?

<samp>That's awfully verbose, but I suppose most people should be able to grok the short-hand anyway. I'd like to hear better suggestions though.

<samp>An empty argument like `2block3!` is an option. The format `2b3!` works well for short too, but I think I prefer `2bb3` to that option, since `2b3!!arg` isn't great without whitespace. The format `2bb3!arg` is marginally better. Also, for speed typing, the double b's are much more preferable.</samp>

<samp>In the following, I will be usin the nicest-looking `2b2!` syntax.</samp>

####  Acceptable outcomes
To determine the number of acceptable outcomes, add up the values of the desired results from this chart.

|Result            |Value|
|------------------|-----|
|Defender Down     |1    |
|Defender Stumbles |1    |
|Both Down         |1    |
|Push              |2    |

> **Example:** A Saurus has a 2-die block against a Dark Elf Lineman. The only acceptable result is Defender Down, which has a value of one. The syntax is `2block1` or `2b1`.

<samp>REQUEST FOR COMMENT: Is this the most intuitive way to deal with acceptable outcomes from a block? Or should it rather be in the format of 1+, 2+, etc.? I often think about block die results in terms of the latter, but I rarely use actual Block Dice in play, so I might be biased.

<samp>Consider also that if something like `2block2+` is to be accepted as an alternative input, it would in all likelyhood have to be an *exception* to the rule that plus signs are optional, unless this would be the default and the length-of-set format non-default. 

<samp>Also, should people be able to enter the desired results precisely, like `1b(DD, DS, BD)`? I don't really see the point in allowing that if we can solve the Block/Tackle issue elegantly, but I *can* see scenarios where counting up the values could actually be slower than just typing it in for inexperienced users.</samp>

####  Negative dice blocks
For blocks where the opponent chooses the result, add a minus sign `-` before the number of block dice, like `-2block` or `-2b`.

> **Example:** A Human Lineman (S3) blocks a Saurus (S4) without any assists. He wants a Push to set up a crowd-surf. The syntax is `-2block2` or `-2b2`.

### Armor Breaks
The syntax for an Armor Break is `AV n+`, where ``n`` is the target number. The keyword `AV` can be shortened to `a`.

> **Example:** A Saurus has AV 9+, so the syntax for the armor break is `AV 9+` or `a9` for short.

The parser assumes that armor breaks are "safe" rolls that cannot result in a turnover.

**Note:** Don't chain an Armor Break after a block that accepts Push results. You will get the wrong results.

### Foul Action
Fouling works exactly like Armor Breaks, except you use the keyword `foul`, with no short form.

The parser assumes that a result of double 1's will result in a turnover, and that rolls below the target number will result in a failure.

### Injury Rolls
The syntax for an Injury Roll is `injury n+`, where `n` is the target number. The keyword `injury` can be shortened to `i`.

> **Example:** You want an annoying player with Stunty off the pitch, so you need a K.O. or better. Consulting the injury table, you see that you need to roll 7+. The input is `injury 7+` or `i7` for short.

The parser assumes that injury rolls are "safe" and will not result in a turnover on a failure.

**Example:** You have a 1-die block that will down the opposing player on a defender down or defender stumbles. You want to know the chances of breaking his AV 7+ and getting a K.O. or better on the injury roll. For this, you could use the syntax:

    1b2, AV 7+, injury 7+; 

**As an argument:** Optionally, you can add the injury roll as an argument to an Armor Break (or a Foul), for example like `AV 7+ !injury 7+` (or `a7+!i7+` for short). This gives a cleaner output, but see the notes on Claw, Dirty Player and Mighty Blow for some caveats and usage details.

In the example above, you could use the syntax:

    1b2, a7!i7; 

**Fouls and injuries:** If an Injury Roll follows a prior Foul Action anywhere in the same player's action sequence, the parser will assume that a result of double 1's will result in a turnover and that rolls below the target number will result in a failure.

### Pass Interference
<samp>REQUEST FOR COMMENT: Maybe something like `1–(n+)`, with `n` being the opponent's chance to deflect? That makes sense if you understand the math behind it (opposite event rule), but maybe not otherwise. Perhaps it should instead be `(n+)` as input, in a vague parallel to how they sometimes use parantheses for negatives in finance?

<samp>You could also use some arbitrary odds `n/6`, if you care to count it out. But that seems a lot less user-friendly to me.

<samp>The same idea applies to e.g. Shadowing. If your line of play requires that an opponent doesn't suceed on Shadowing, it should be possible to denote that in an intuitive fashion. A general way to take any expression and turning it into `1-<expr>` would be useful.</samp>

### Arbitrary Odds
<samp>REQUEST FOR COMMENT: Probably `n/d`, with `n` as the numerator and `d` as the denominator. Optionally with an argument to make it safe against Turnovers, or maybe that should be default behavior. It's mostly for bounce results, yeah? In theory you can analyze weather and kick-off events too, but that seems very niche.

Scatter and bounce could get spicy withl logical XOR as a possible operator. If you could input something like "I either catch it or it bounces to one of these squares", the utility goes way up for e.g. analyzing certain passes.
</samp>

## Advanced Features

### Multiplication
You can group a number of identical actions by the same player into one command by multiplication:

    2*4+, 3*2+;

> This example could be two 4+ dodges, followed by three 2+ Rushes by the same player (with Sprint).

This syntax is equivalent to:

    4+, 4+, 2+, 2+;

You can use parentheses to group and multiply multiple actions:

    2*(4+, 2+);

This syntax is equivalent to:

    4+, 2+, 4+, 2+;

You can use the letter `x` instead of `*` if you prefer.

### With multiple players
You can group and repeat an identical series of actions with different (but functionally identical) players, you need to enclose the entire activation in parentheses, including the semicolon:

    2*(4+, 2+;)

> This example could be one 3+ dodge with the Dodge skill, followed by a 2+ rushe by the same player. Then, a different player attempts the same sequence of actions.

This syntax is equivalent to:

    4+, 2+;
    4+, 2+;

You can also nest multipliers:

    2*(4+, 2*2+;)

This syntax is equivalent to:

    4+, 2+, 2+;
    4+, 2+, 2+;


### Logical operators

By default, the parser assumes a logical AND operator and operates conjunctively. But you can force the use of a logical XOR operator with a pipe character `|`, which is useful in certain situations.

This will force the calculator to use the exclusive disjunctive formula `P(A xor B) = P(A) + P(B) − P(A and B)` instead of the conjunctive formula `P(A and B) = P(A) * P(B)` for that particular expression.

In simpler terms, this means the probability of either event A or event B occuring, but not both.

**Example**: Let's say you need to get an opposing player without Block away from his square. You have a player with the Block skill and a two-die block on that player.

The player can be taken away from the square in one of two mutually exclusive ways:

1. by pushing the player away from the square, or 
2. by removing the player with an injury roll of 8+ on a Both Down.

What are the odds of either one or the other happening?

The first outcome only requires a roll of defender down, defender stumbles or push. So you could use the syntax:

    2b4!

The second outcome requires a result of Both Down followed by an armor break and an injury roll of 8+. Assuming AV 7+, you could use the syntax:

    2b1!, a7+!i8+;

To represent the odds of either one *or* the other of these events occuring, you can use the logical XOR operator:

    2b4! | 2b1!, a7+!i8+;

The output would look something like this:

    ========================================
    2D Block! [4/6]
    ========================================
    OR:
    ========================================
    2D Block! [1/6]
    ----------------------------------------
    Armor break 7+, injury 8+
    ========================================
                                      {Odds}
    ========================================

With `{odds}` calculated by exclusive disjunction.

By default, the XOR operator changes the behavior of the entire action sequence of the given player. If you only want to use it for some parts of the sequence but not others, you will need to use parentheses.

For example, the following input would be treated as a player attempting either two consecutive 4+ rolls *or* two consecutive 2+ rolls:

    4+, 4+ | 2+, 2+;

If you want the sequence to instead be 4+, and then either a 4+ or two consecutive 2+ rolls, you will need to add parantheses like this:

    4+, (4+ | 2+, 2+);

If you want it to be a single player attempting a 4+ roll, and then either a 4+ roll or a 2+ roll, followed finally by another 2+ roll, you will need to add parentheses like this:

    4+, (4+ | 2+), 2+;

**Example 2:** Another example of when you might want to use XOR logic could be a passing play where the intended target of a pass has adjacent teammates that can catch the bounce if the receiving player doesn't catch ball.

Let's say your Dark Elf Thrower is passing a quick pass to a dark elf lineman next to another dark elf lineman and nobody else. The chance of making the pass is 3+ and the chance of catching it is 3+. You could use the syntax:

    pass 3+;
    3+;

But if you fail the catch, you would also get a 1/8 chance for the adjacent player also getting an attempt at catching the ball, this time on a 4+ since it's bouncing. So for this outcome, you could write:

    1/8, 4+;

Now, if you want the chance of either the first or the second mutually exclusive events occuring, you could use the syntax:

    pass 3+;
    (3+;) | (1/8, 3+;)

Note the parentheses.

### Ditto mark
You can use the flag `ditto` or `"` to repeat the last action, including all arguments. This is rarely more efficient than using multiplacation, but it has some uses.

For example, you might want to use a ditto mark in a situation with a Frenzy player trying to get a K.O. on a defender down or defender stumbles with two block dice on both potential blocks:


### Logical or

<samp>REQUEST FOR COMMENT: Is there ever a need for this?</samp>


## Other Skills

### Dauntless
Dauntless can be added as a flag with the syntax `dauntless n+`, where `n` is the target number required to get one extra die on the block. The short form is `dl`.

The Dauntless rule will be calculated for all subsequent blocks in the action sequence. The calculation will correctly apply exclusive disjunction, but it will not display as such.

An input like:

    dauntless 4+, 1b2;

would give an output like:

    ---------------------------------------
    1D block, 2D w/dauntless 4+ (2 in 6) 
    ---------------------------------------

**Note:** If the player also has Multiblock, you may need to correct the value of `n` by adding more `dauntless` flags to the sequence at the correct position.

By default, the parser assumes that you will *not* reroll a failed Dauntless roll with a team re-roll. But when added as a flag, Dauntless can take an empty argument `!`, which tells the parser that you *are* willing to reroll the result with a Team Reroll.

You can also pass Dauntless in as an argument to a Block Action, e.g.: `1b4 !dauntless 4+;` or `1b4!dl4`.

### Dirty Player (x)
Dirty Player can be added as a flag with syntax `dirty player x`, where `x` is the value of the skill. The short form is `dp`. If omitted, `x` defaults to `1`. 

Adding Dirty Player as a flag will affect the next Foul and Injury Roll in that player's action sequence.

Dirty Player can also be added as an argument to Foul Actions and Injury Rolls, but that's usually just more verbose.

<samp> REQUEST FOR COMMENT: Can you do a Blitz+Foul? Foul+Blitz?</samp>

### Diving Tackle
The effects of an opposing Diving Tackle can be added either as a flag or as an argument to a normal roll or a dodge skill roll.

The keyword is `diving tackle` in both cases. This can be shortened to `dt`.

For example, if your player attempts a 3+ dodge away from an opposing player with Diving Tackle, you can use either `dt, 3+;` or `3+!dt`.

If multiple dodge attempts would be affected by Diving Tackle, you might want to use e.g. `dt, 3+, 3+;` instead of `3+!dt, 3+!dt;`.

If only the last dodge attempt would be affected by Diving Tackle, you could use e.g. `3+, dt, 3+;` or `3+, 3+!dt;`.

If only the first dodge attempt would be affected by Diving Tackle, you will need to pass in the argument, like `3+!dt, 3+;`.

The parser assumes that the opponent will use the skill optimally in a generic sense (force a reroll if possible). But since there's a chance you might be forced to reroll *without* the use of Diving Tackle anyway, the opponent will add it to the *final roll* that percentage of the time.

**Note:** If you have no team re-rolls and no other source of a re-roll for the dodge attempt, you can ignore Diving Tackle and instead adjust the target number by +2 manually.

### Jump Up
There's no special syntax for Jump Up. You can simulate its effect using a normal action and a block. The parser doesn't enforce the number of "blitzes" you can have in a turn.

Example: Dave the Orc wants to Jump Up and punch an orc in the face, hoping for Defender Down on a two-die block. Use a syntax like `5+, 2b1;`.

### Frenzy

There's no special flag or argument for Frenzy, but properly representing the skill will sometimes require the use of the logical OR operator.

**Example:** Let's say you want to know the odds of getting at least a K.O. on a target with AV 7+ on your one-die blocks from a player with Frenzy.

This can happen in one of two mutually exclusive ways. You either 1) succeed on the first block (DD or DS), or 2) get a Push on the first block and then succeed on the second block.

The first outcome can be notated `1b2`. The second outcome can be notated `1b2, 1b1`.

To represent the odds of either one *or* the other outcome on the block dice, you could write:

    1b1 | 1b2, 1b1;

Then you can add the armor break and the injury roll at the end of the sequence, like this:

    (1b1 | 1b2, 1b2), AV 7+, injury 8+;

Now, if the action was a Blitz and the Frenzy player needs to make a Rush in order to make the second Block, you will need to add that expression to the right-hand side of the operator as well.

    (1b1 | 1b2, 2+, 1b2), a7!i8;

If the blitz requires *two* rushes, one before and one after the block, the syntax would be:

    2+, (1b1 | 1b2, 2+, 1b2), a7!i8;

If you don't care about the injury and armor breaks, you can of course simplify this to `1b1 | 2b2, 1b2;`, `1b1 | 2b2, 2+, 1b2;` and `2+, (1b1 | 1b2, 2+, 1b2)` respectively.

**Note:** If the defending player has Dodge and you don't have Tackle, remember that the chance of a push is actually 3 and not 4.

<samp>REQUEST FOR COMMENTS: Is it possible to come up with some unique syntax that will cover the most common case here? Maybe have a keyword `frenzy` that takes the block dice on the left side of the operator and recreates the "push,push" chance programmatically on the right? The next term could perhaps also be programmatically generated, unless the player knows they're in a Frenzy Trap or Frenzy Assist scenario.</samp>

### Shadowing

<samp>VERY MUCH NOT DECIDED HOW TO IMPLEMENT, BUT HERE'S A START:
Shadowing can be added as a flag or an argument with `shadowing n+`, `shadow n+` or `sha n+` to any normal roll or dodge roll, where `n` is the result required for the shadowing roll to succeed.</samp>

<samp>The result of the Shadowing attempt will not effect your success rate or your turnover rate in any way. It's only purpose is to to be used with exclusive disjunction.</samp>




## Special Rules

## Argue the Call
You can add `argue the call` or `atc` as a flag to a player's action sequence. It should properly effect all subsequent Foul actions and Injury Rolls in that player's action sequence.

## Bribe
You can add `bribe` as a flag to a player's actions sequence. It should properly effect all subsequent Foul actions and Injury Rolls in that player's action sequence.
* * *
*// How do multiple bribes work? If I get snake-eyes twice on a foul, can I use two bribes?*
* * *

## Appendix

### Label Chart

|Label | Symbol     |
|-------------------|
|Rush: | r: 
|Dodge | d:
|

