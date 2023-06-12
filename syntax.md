


## 1. Introduction

RiskyPlay input consists of a series of comma-delimited commands that represent actions, for example:

**Example:**
```
d4, h2, f2, 2b4
```

To add actions from more players, replace the comma separating them with a semicolon.

**Example:**
```
d4, h2, f2, 2b4;
d4;
d4, f2, f2;
```
> This example would be interpreted as three different players performing these actions.

The interpreter ignores whitespace and line breaks, so the code above could be rewritten as:

```
d4,h2,f2,2b4;4;d4,f2,f2
```


## 1. D6 Rolls

The syntax for a normal d6 roll is n, where n is the target number.

**Example:** A human lineman attempts a 4+ dodge out of a tackle zone. The syntax is ``4``.

Use this syntax when the player in question doesn't have a skill that lets you re-roll the result.

## 1. Skill Rolls
There are five re-roll skills in the game: dodge, pass, catch, sure hands, and sure feet.

**Long form syntax**
```
dodge 4, pass 4, catch 4, sure hands 4, sure feet 4.
```

**Short form syntax:**
```
d4, p4, c4, sh4, sf4
```

You can optionally omit the `s` in ``sh`` and ``sf``, yielding e.g.
```
h4, f4
```





1. Block Dice


1. New Player

## 1. Introduction

## 1. The Basics

