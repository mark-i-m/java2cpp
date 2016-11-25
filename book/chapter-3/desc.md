# Freddy the Chipmunk

Ok, now is a good time for an example.

Let's implement some sort of game that serendipitously uses nearly everything I
just wrote about.

The world is a grid of items. There are three categories of items: office
supplies, kitchen appliances, and food. The office supplies can be applied to
other items. The kitchen appliances can be used to produce food of some sort
(we'll count drinks as "food" too for simplicity).  Kitchen appliances and food
can be either used or sold. Kitchen appliances and office supplies can be
bought. Food must be produced.

Your goal is to create a path across the world that is paved with fresh toast
for Freddy the Chipmunk to find his way home. The path needs to be continuous
and the toast pieces must be adjacent to each other. Moreover, Freddy is not
very smart, so every square of toast on your path needs to be marked with a
direction. Also, Freddy doesn't like to get his paws wet, so the path cannot
contain coffee-soaked pieces of toast. Finally, there was a tornado in the
office lounge so that items of all sorts have been strewn across the world.

The world contains only a few items and rules of interest:
- Adjacent squares include only the squares at the four cardinal directions.
- There can be at most one item in a square. The second item to be placed
  somewhere is discarded.
- An item will block the follow of coffee from an adjacent coffee pot in its
  direction, but it will be ruined so you cannot use it for its normal purpose.
- A permanent marker is used to mark a direction on items in adjacent squares.
  This has no effect except on toast.
- A coffee maker produces coffee in each adjacent direction.
- A toaster produces toast in each adjacent direction.
- A piece of toast is used if it is part of a path.
- Coffee is automatically spilled wherever it is produced.
- Each item has a cost to buy and a benefit if sold, according to the following
  table.
- Items can only be sold if they are not used or ruined.
- Grid items are processes in row-major order from top to bottom, from left to
  right.

Item                | Cost to buy | Value if sold
--------------------|-------------|--------------
Marker              | $1          | N/A
Toaster             | $50         | $0 
Coffee Maker        | $25         | $50
Toast               | N/A         | $0.50
Coffee              | N/A         | $0

Your path's point value is inversely proportional to its net cost.
