# ECE-287-Flappy-Bird-Project
Flappy bird game created in verilog
This is our Read Me and final project report.

ECE 287 Project

Tanner McWhorter,Casey Killeen

A Flappy Bird inspired obstacle dodging game.

A short demonstration is show in the following link:
https://www.youtube.com/watch?v=ybvcwc43neQ

Project Description:

Our project was to create a version of the notorious flappy bird game in hardware. Flappy Bird is a phone game that consists of a bird that is constantly falling, unless the user presses a button. The bird needs to make it through each “hole” between the pipes in order to avoid being sent back to its original position. We wanted a fairly simple game that tested our ability to write Verilog and work with Quartus software. The benefit of Flappy Bird is that there is only one input to play the game, however we added inputs for pause and reset. There were three main problems that we encountered while working on this problem. First we had to come up with a way to continually generate walls that the bird had to fly through. Second, we needed a way to detect collision. Third, we wanted to find a way to create the hole positions in a way that wasn’t the same each time. We attached our initial proposal above, in order for everyone to see what we were seeking to achieve.

Project Design:

Our design consists of a side scrolling background that continuously wraps from one side of the screen to the other. Our best solution for continuously creating the walls was to have a finite number of bars wrap around the screen. So a bar will start at the right side of the monitor and once it hits the left side of the monitor, it will jump back to the right side. We actually only draw 5 pairs of walls that continually go across the screen. Once the walls are sent back to the right side of the screen, the hole locations change giving the appearance that there are an infinite number of walls with random hole placement. The bird object (the red square) simply has vertical movement that is controlled by the spacebar on a PS2 keyboard plugged into the DE2 board. We placed blue objects on the top and bottom of the screen that send the bird back to the original position if contact is made with them. This is to ensure that the bird does not go off the screen and out of range. Our greatest challenge was being able to understand what each part of the VGA module was doing on the screen. We had to understand this module in order to create movement and collision detection. Our greatest leap towards these problems were achieved through a signal tapping program to see what the objects were doing at each step. We implemented a reset switch to set all of the objects back to their original horizontal positions. When the bird makes contact with either the top of the screen, the bottom of the screen, or any of the moving bars, the bars are set back to their original horizontal positions and the bird is set to its default position. We wanted to create a reset stage to make things easier to test. This is the SW[16] switch on the DE2-115 board. When this switch is on, the bird will stay in the starting position until it is flipped off. We also added a pause switch which is the SW[15] switch. This stops the bird in its current position whenever the switch is on. When flipped back off, the bird will continue to move regularly. We used a 1280X1024 60Hz monitor.

High Level Description:

Our project simply consists of objects being drawn on a monitor through a VGA port. With the VGA module that we used, the monitor screen was layed out on a coordinate grid with the top left corner being (0,0). To draw an object, we first had to set the point as to where we are going to reference it. The objects width and height is also determined in this step. This is done in the objects parameters. We needed this to be at the left most side of the screen for all of our moving bars, because if not, then our wrapping solution wouldn't work correctly. The objects are defined at their top left corners. The objects each have a variable for their x and y positions. From here the object can be placed anywhere on the screen. Next, the objects get drawn on the screen in the priority that we chose. If we were to program the board from here, the objects would still be drawn to the monitor, but you wouldn't be able to see them. This is the case, because the objects havn't been colored in yet. Once the objects are prescribed a color, they will appear on the screen. Our movement was based on a counter that we created. Once the counter hit a certain value, we would subtract or add to the x or y variable for that object. The vertical movement for the bird is controlled by a combination of the user input and the counter. We were able to create wrapping by comparing the x variable to the left most side of the screen and if it hits that position, then we added to the x varibale to make it jump to the other side. Also, as the bars wrap from one side to another, we set different values for the top and bottom wall y variable values. Our collision detection is very simple. It consists of an if statement that compares whether or not two objects are drawn to the same place at the same time. If there is ever any collision, we set the objects back to their original position. In order to create a reset stage, we set a switch to create constant collision, which keeps the objects at their original positions. The pause state was created by implementing a switch that will only allow movement when it's off. We have posted snippets of the project to demonstrate what each part does above.

Project Results:

We were able to better organize the VGA out port module, which made it easier to control movement for each individual object. We kept all of the movement for each object within the same location, in order to make it very simple to control how it moves and wraps.
This allowed us to change an objects movement, such as side scrolling, wrapping, and bird movement, all within the same location. Having all of an objects movements together really made it easier for our trial and error process. Overall, we were able to build a game that resembles flappy bird.

Conclusion:

We were able to successfully implement a hardware description that builds a game of Flappy Bird with an FPGA board. This project taught us a lot about VGA out port. The actual logic for the game wasn’t nearly as complicated as figuring out how the VGA works with our specific module. Very small syntax errors and the location of the logic makes a big difference on the final results.

Citations:
//keyboard
From Funkheld on http://www.alteraforum.com/forum/showthread.php?t=46549
We received it from Isaac Steiger
  input keydata;
  input keyclk;
  reg [7:0] keycode;

parameter idle    = 2'b01;
parameter receive = 2'b10;
parameter ready   = 2'b11;

reg [7:0] previousKey; 
reg [1:0]  state=idle;
reg [15:0] rxtimeout=16'b0000000000000000;
reg [10:0] rxregister=11'b11111111111;
reg [1:0]  datasr=2'b11;
reg [1:0]  clksr=2'b11;
reg [7:0]  rxdata;


reg datafetched;
reg rxactive;
reg dataready;

always @(posedge clk ) 
begin 
  if(datafetched==1)
  begin
  	 if(previousKey != 8'hF0)
	 begin 
		keycode<=rxdata;
    end
	 previousKey <=rxdata;
  end
end  
  
always @(posedge clk ) 
begin 
  rxtimeout<=rxtimeout+1;
  datasr <= {datasr[0],keydata};
  clksr  <= {clksr[0],keyclk};


  if(clksr==2'b10)
    rxregister<= {datasr[1],rxregister[10:1]};


  case (state) 
    idle: 
    begin
      rxregister <=11'b11111111111;
      rxactive   <=0;
      dataready  <=0;
		datafetched <=0;
      rxtimeout  <=16'b0000000000000000;
      if(datasr[1]==0 && clksr[1]==1)
      begin
        state<=receive;
        rxactive<=1;
      end   
    end
    
    receive:
    begin
      if(rxtimeout==50000)
        state<=idle;
      else if(rxregister[0]==0)
      begin
        dataready<=1;
        rxdata<=rxregister[8:1];
        state<=ready;
        datafetched<=1;
      end
    end
    
    ready: 
    begin
      if(datafetched==1)
      begin
        state     <=idle;
        dataready <=0;
        rxactive  <=0;
		  datafetched <=0;
      end  
    end  
  endcase
end 

We received our VGA module from Parth Patel, Ian Baker, Yi Zhan.
