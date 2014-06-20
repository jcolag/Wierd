Wierd
=====

This is an interpreter for the the Wierd Programming Language, a MicroFunge.

History
-------

The long-defunct _Befunge Mailing List_ once questioned if _Befunge_ was too "top-heavy" of a language--that is, if its instruction set was needlessly large.  This, as one might expect (especially if said one is reading this specification), quickly degenerated into an argument of "my language is smaller than yours."  Ben Olmstead managed to trim down Befunge to a lean seven instructions, dubbing the creation _BeF***_.

Not to be outdone, it was quickly suggested by myself (John Colagioia) that instructions, per se, be eliminated in favor of the "visual programming" aspect of Befunge:  The angles made by sequences of instructions.  Such a language, of course, would have a single "instruction" from the syntactic standpoint, although it surely cheats by horribly abusing the concept of Euclidean geometry.

Chris Pressey then jumped on it, suggested the angle-to-instruction mapping, and christened the entire mess "Wierd"--a cross between the words "weird" (which the language seemed to be) and "wired" (which would describe the appearance of programs written in the 
language).

The Basics
----------

Wierd, like Befunge, is a two-dimensional language with a rectalinear grid of instructions which manipulate a system stack area.  Where Befunge has discrete, textual instructions, however, Wierd has a more implicit instruction set, each instruction denoted, not by a symbol, but by a configuration of symbols relative to the current direction of the instruction pointer:  the angle of deviation from "straight ahead" from the IP's perpsective.  That is, reading from left to right, the following program contains a single instruction.

    ******
          *
           *
            *

The instruction is interpreted by a reader as a 45-degree angle to the right.

The Details
-----------

A Wierd source file can be any file, as the language only distinguishes between two symbols:  Whitespace and everything else.  A chain of non-whitespace symbols makes a Wierd program.

Execution begins with the first character of the file (typically considered to be the "upper left" of the program), and the initial direction of the Instruction Pointer is down and to the right.  This was chosen to allow the programmer to get away from the corner as efficiently as possible, which reduces the risk of the program becoming
trapped.

Newlines are defined in a system-dependant manner, though it can be safely assumed on most systems that a carriage return and/or line feed character delineates the end of each line and begins the next.

Execution then passes from character to character, in the specified direction until a turn needs to be made.

Inertia
-------

Like water, the Wierd IP takes the path of least resistance when confronted with a choice.  If the Wierd program branches, the IP will always choose the path which is closest to the direction it is already travelling.  Therefore, see the following code. 

    ***********
         *
          *
           *

Assuming that the IP starts at the left and travels to the right, as described previously, execution will stay on the top line, and never travel down the branch shown.

Should the IP be faced with two equally-likely choices, the branch chosen by the interpreter is undefined, except for one condition which will be treated later.

_(Though, as an implementation note, it should be pointed out that the only existing interpreter at this time chooses the left-hand path.  Note that Wierd was not intended to be left-handed, specifically; this is just how the coin showed during the design phase...literally)._

Gap-Jumping
-----------

While the above is suitable for a fully-featured programming language, it was quickly realized that programmers could easily (possibly certainly) "paint themselves into a corner," where programs beyond a certain complexity might simply be impossible to write, and would at least be extremely impractical.

Therefore, in keeping with the pseudo-electronic motif, the Instruction Pointer was given the ability to launch itself over a limited distance of unused ("empty") programspace to attach to a new program segment.

This jumping, to keep it only used in emergency situations, is only usable over a maximum distance of two cells in the program grid.

Gap-Jumping follows the same rules as typical movement, regarding _Inertia_.  That is, the IP will choose paths to jump into by how much of a change of direction is required to reach it.

Note that jumping a gap into an isolated instruction, like in the following code segment:

    ***** *

is considered an error.

The Instructions
----------------

The Wierd instruction set is based on that of Befunge, though reduced to a nearly-absurd minimalism, and with a certain amount of "stack abuse," in addition to the abuses already heaped upon the program's geometry.

While the People for the Ethical Treatment of Data Structures threatened to protest, they quickly realized that their acronym was not easily pronounced, and retreated to debate the merits of a new name before further business could be discussed.

The instructions, listed by angle, are:

| **Angle** | **Opcode** | **Description** |
| ------------ |:------:| ----------- |
|   0&deg;  | `NO`   | No operation, continue as normal. |
|  45&deg;  | `P1`   | Push a data value of 1 onto the stack. |
|  90&deg;  | `IF`   | Pop the stack.  If the value is zero, continue executing as normal.  If the value is nonzero, however, reverse direction. |
| 135&deg;  | `GP`   | Pop the stack.  If the value is zero, pops the next two items from the stack, retrieves (gets) the value stored at the coordinates specified by these values (x, then y), and push it onto the stack.  If the first value was nonzero, however, takes the value stored below the coordinates on the stack, and stores (puts) it at the coordinates. |
| 180&deg;  | `QU`   | Jump the gap, if possible.  Otherwise, terminate. |
| 225&deg;  | `IO`   | Pop the stack.  If the value is zero, read a character from input, pushing it onto the stack.  If the value was nonzero, pop the stack, and print the value to output as a character. |
| 270&deg;  | `IF`   | See 90&deg;.  Included for flexibility. |
| 315&deg;  | `SB`   | Subtract the top of the stack from the value beneath it, popping both values, and pushing the result. |

Note that pushing a `1` is the only form of direct data specification available to the developer, and subtraction is the only available arithmetic operation.  If the programmer wants fancy-pants operations like addition or division, they need to be programmed.

Concurrency
-----------

The exception mentioned above to the "always choose a single path" rule is when the two likeliest paths are at angles of both 90 and 270&deg;--both forms of conditional.  In such a case, rather than choosing one or the other, both paths are chosen, and the IP is cloned (including stack) to cover the other path.

Implementation Notes
--------------------

As with a lot of these "small language" implementations, this is extremely old code (1997) that almost certainly does unsafe things.  I wouldn't recommend using it for anything mission-critical.  I mean, I made a function table the centerpiece of the language run-time.  On purpose!

There are several deviations from the "Official" Wierd:

 - __Direction__:  The IP will always try to continue in the same direction (or as close as possible).  When two equally-likely possibilities exist, it will choose the "leftmost" branch.

 - __Conditionals__:  When the IP connects to a 90-degree angle, it pops the stack.  If the result is zero, it continues as if nothing interesting happens.  If nonzero, it reverses the IP direction.

 - __Gap-Sparking__:  If the IP has just "sparked into" a location which is isolated, the program is considered erroneous, as the IP direction is undefined at that time.

I also vaguely recall a pushy user demanding that I fix bugs, including several tirades against having a program that printed ASCII values and didn't skip the beep.  I may have done so.  I may have done so and never released the code.  I probably didn't.  I _know_ the ASCII program was never "fixed," because it's not broken.  Either way, expect the bugs to still be there until I fix them.

If you stumble across them, hey, look, GitHub has a bug-tracker!
