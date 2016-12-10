# ECE-287-Flappy-Bird-Project
Flappy bird game created in verilog
This is our Read Me and final project report.

ECE 287 Project

Tanner McWhorter,Casey Killeen

A Flappy Bird inspired obstacle dodging game.

Project Description:

Our project was to create a version of the notorious flappy bird game in hardware. Flappy Bird is a phone game that consists of a bird that is constantly falling, unless there is user input, and the bird needs to make it through each “hole” between the pipes. We wanted a fairly simple game that tested our ability to write Verilog. The benefit of Flappy Bird is that there is only one input to play the game, however we added inputs for pause and reset. There were three main problems that we encountered while working on this problem. First we had to come up with a way to continually generate walls that the bird had to fly through. Second, we needed a way to detect collision. Third, we wanted to find a way to create the hole positions in a way that wasn’t the same each time. 

Project Design:

Our design consists of a side scrolling background that continuously wraps from one side of the screen to the other. Our best solution for continuously creating the walls was to have a finite number of bars wrap around the screen. So a bar will start at the right side of the monitor and once it hits the left side of the monitor, it will jump back to the right side. We actually only draw 5 pairs of walls that continually go across the screen. The bird object simply has vertical movement that is controlled by the spacebar on a keyboard. Our greatest challenge was being able to understand what each part of the VGA module was doing on the screen. We had to understand this module in order to create movement and collision detection.  Once we were able to work with the module, we were faced with the problems of wrapping and collision detection. Our greatest leap towards these problems was achieved through a signal tapping program to see what the objects were doing at each step. We then had to deal with what will happen if the bird went above the resolution of the screen. We were able to force the bird to never go above or below the screen by counting it as a hit and setting everything back to their original positions. We implemented a reset switch to set all of the objects back to their original positions. When the bird makes contact with either the top of the screen, the bottom of the scree, or any of the moving bars, then the bars are set back to their original horizontal positions and the bird is set to its default position. This is the SW[16] switch on the DE2-115 board. We also added a pause switch which is the SW[15] switch. We used a 1280X1024 60Hz monitor. In order to add to the complexity of the problem, we added the user input to come in through a PS2 keyboard space bar.

Project Results:

We were able to better organize the VGA out port module, which made it easier to control movement for each individual object. We kept all of the movement for each object within the same location, in order to make it very simple to control how it moves and wraps. This allowed us to change an objects movement, such as side scrolling, wrapping, and bird movement, all within the same location.

Conclusion:

We were able to successfully implement a hardware description that builds a game of Flappy Bird with an FPGA board. This project taught us a lot about VGA out port. The actual logic for the game wasn’t nearly as complicated as figuring out how the VGA works with our specific module. Very small syntax errors and the location of the logic makes a big difference on the final results.

Citations:
