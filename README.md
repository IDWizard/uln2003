# uln2003
Micropython code to drive stepper motors via ULN2003

This is the ongoing work of my experiments with the BBC micro:bit and some ULN2003 stepper motors connected to 5V 28BYJ-48 Stepper Motors.

## Materials

You will need:

1. A micro:bit with USB cable (and a computer to connect it to!)
1. A 5V DC power source
1. One or more 28BYJ-48 stepper motors with matching ULN2003 driver boards
1. Wires, lots of wires.
1. (Optional but handy) A breakout board to make accessing the bit's IO pins easier

## Wiring / Connecting

1. Plug the stepper motor into the ULN2003
1. Connect the 4 pins from the ULN2003 into 4 separate I/O pins of the bit
1. Connect the 5v input +/- on the ULN2003 to a 5v source +/- terminals
1. Connect the bit to the computer
1. Load up the library and push to the bit (i.e. compile the program and upload to the bit)

## Using the library

Code example:

```python
# Create a stepper using the HALF_STEP command sequence 
# to a stepper which is connected:
#                     micro:bit    ULN2003
#                     pin16     -> INP1 
#                     pin15     -> INP2
#                     pin14     -> INP3
#                     pin13     -> INP4
# Set the delay between steps to 5 microseconds

s1 = Stepper(HALF_STEP, microbit.pin16, microbit.pin15, microbit.pin14, microbit.pin13, delay=5)  
s1.step(100)     # Rotate 100 steps clockwise

s1.step(100, -1) # Rotate 100 steps anti-clockwise
```

A delay of less than 5 microseconds may cause the motor to simply buzz and not move at all. Try and see what works for you and your motors.

Because of the gearing of this motor a full rotation isn't very exact. It is somewhere between 508 and 509 steps. The constant FULL_ROTATION has a value of int(4075.7728395061727 / 8) = ~509 steps.

## Advanced Usage (Driving more than 1 stepper motor)

Being able to drive a stepper is cool but the bit has a lot more IO pins. It's possible to drive two steppers:

```python
s1 = Stepper(HALF_STEP, microbit.pin16, microbit.pin15, microbit.pin14, microbit.pin13, delay=5)    
s2 = Stepper(HALF_STEP, microbit.pin6, microbit.pin5, microbit.pin4, microbit.pin3, delay=5)   
s1.step(FULL_ROTATION)
s2.step(FULL_ROTATION)
```

But you will notice that this makes stepper1 move a full circle (or a teensy bit more than a full circle) and then stepper2 will move a full circle. 

If you want to move both motors at the same time then you need to interleave the commands to do that so that stepper1 moves a step, then stepper2 moves a step, then stepper1 moves a step and so on.

The library provides a way to do that:

```python
s1 = Stepper(HALF_STEP, microbit.pin16, microbit.pin15, microbit.pin14, microbit.pin13, delay=5)    
s2 = Stepper(HALF_STEP, microbit.pin6, microbit.pin5, microbit.pin4, microbit.pin3, delay=5)   

c1 = Command(s1, FULL_ROTATION)         # Go all the way round
c2 = Command(s2, FULL_ROTATION/2, -1)   # Go halfway round, backwards

runner = Driver()
runner.run([c1, c2])
```

The Driver class will run 1 step from each command until there are no more commands to run which will make the two motors move (apparently) simultaneously.



