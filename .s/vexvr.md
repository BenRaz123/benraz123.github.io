<style>
	:root {
		--accent-color: orange
	}
</style>


# Vex VR 

Ben Raz

---

## Getting Started

- Go to <https://vr.vex.com>
- Click Continue to Free
- Go to File>New Blocks Project
- Place the turn to heading 90 degreed and drive forward for 250mm block
- Click Play

---

## Using Code Instead

NOTE: Only works for if you are me because I for some reason have access to premium

- Go again to <https://vr.vex.com>
- Click Continue to Free
- Go to File>New **Text** Project

---

## The Code I Wrote

This code makes turning the machine way simpler

```python
def interpret_char(c):
    if c == 'r':
        drivetrain.turn_to_heading(90,DEGREES)
        drivetrain.drive_for(FORWARD, DEFAULT_DISTANCE, MM)
    elif c == 'l':
        drivetrain.turn_to_heading(-90, DEGREES)
        drivetrain.drive_for(FORWARD, DEFAULT_DISTANCE, MM)
    elif c == 'u':
        drivetrain.turn_to_heading(0, DEGREES)
        drivetrain.drive_for(FORWARD, DEFAULT_DISTANCE, MM)
    elif c == 'd':
        drivetrain.turn_to_heading(180, DEGREES)
        drivetrain.drive_for(FORWARD, DEFAULT_DISTANCE, MM)
    elif c == 'f':
        drivetrain.drive_for(FORWARD, DEFAULT_DISTANCE, MM)
    elif c == 'F':
        drivetrain.drive_for(FORWARD, DEFAULT_DISTANCE*2, MM)
    elif c == 'b':
        drivetrain.drive_for(REVERSE, DEFAULT_DISTANCE, MM)
    else:
        raise Error("Expected one of the following: 'rludfFb'")
```

--- 

# Line by Line

---

## First Line

<span class="bigmarker"></span>
```python
def interpret_char(c):
```

- `def` means that i am declaring something called a _function_. A function defines a piece of code that I can give a name to and reuse.
- `interpret_char` is the name of my function. Now, I am not the best at naming functions, but this name means that this function takes a `character`, which is computer speak for a letter, and interprets it.
- `(c):`: This is the thing that the function takes in. I call it `c`.

---

## Second Line

<span class="bigmarker"></span>
```python 
if c == 'r':
    drivetrain.turn_to_heading(90,DEGREES)
    drivetrain.drive_for(FORWARD, DEFAULT_DISTANCE, MM)
```

- This line checks is the character this function receives is `'r'`. R means to turn right and then go forward one. This is more or less what the code inside of the `if` statement does.

---

## Rest of the Lines Summarized

- `l` => Turn left and move one forward
- `u` => Turn upwards and move one forward
- `d` => Turn downwards and move one forward
- `f` => Move one forward
- `F` => Move _two_ forwards
- `b` => Move one backwards

---

## What this means

Instead of writing something like
<span class="bigmarker"></span>
```python
move_forward()
move_forward()
move_leftward()
move_forward()
move_rightward()
move_forward()
```

You can write
<span class="bigmarker"></span>
```python
# Don't worry about this line
runs = lambda s: [interpret_char(c) for c in s]

runs('Flr')
```
