# Brainstorming

So how do we want to implement this in C++?

Well, let's break down the various classes.

We will probably want an `Item` class, with the world being an array of
`Item*`.

As for the class heirarchy, the following seems to come to my mind given the
problem description:
```
                          Item
                 ---------/ | \---------
                /           |           \
    KitchenAppliance   OfficeSupply     Food
        |       \      /        |       |  \
     Toaster  CoffeeMaker   Marker  Coffee  Toast
```

Also, there are a few properties that all items seem to have:
- `was_used` has the item been used?
- `is_ruined` is the item ruined by a coffee spill?
- `is_marked` is the item marked with a direction?
- `direction` what direction is the item facing?
- `is_original` is the item one of the tornado-strewn items, or something you
  bought.

`KitchenAppliance` seems to have the following method:
- `Food* produce()`

Bare with me, now. I know that this may be a more convoluted implementation
than necessary, but I am trying to make this a good example.

For simplicity, we will define levels in X.freddy files and have the user save
their answer in X.path files, for various X. Also, we will avoid doing a lot of
the input validation steps to keep things from getting boring.
