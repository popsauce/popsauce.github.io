---
author: "popsauce"
title: "Hardest Problem on Codeforces"
date: ""
description: "Solving Tighten Up"
tags: [
    "scripts",
    "problem solving",
    "deep dive",
]
categories: [
    "Scripts",
]
series: ["Hardest problem on codeforces"]
aliases: ["solving-tighten-up"]
---

What makes a problem hard to solve ?

Are hard problems really that out of reach for a person new to CP (Competitive Programming) ?

Since the general advice to tackle CP is that you should work your way up to harder problems.(i.e Solve a lot of easier problems then slowly start solving problems that are harder)

If I were to attempt a problem that was really hard would i be able to solve it ?

Would it be a question of how much time it would take ? (like maybe i'll take more time compared to a seasoned competitive programmer)

Or would my solution not be as efficient ? (read skill issue)

Or god forbid i was unable to arrive at any solution

Anyways after this chain of thought I think you can see where i'm going with this.

<!--more--> 


## Finding the hardest problem on codeforces

There are a lot of ways to classify how hard a problem is, I settled with a straightforward approach for this one and wrote "hardest problem on codeforces" on my search engine and clicked on the first search result, and voila it led me to this [blogpost](https://codeforces.com/blog/entry/10690) which described this [problem](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=1164).
And more than the title it was the problem itself that had me hooked, one reason was that trying to solve this problem in a physical setting with physical props seems really easy so would it really be that hard to solve, in an iterative manner.

Regarding the credibility of this problem being one of the hardest to solve you'll have to take [rng_58](https://codeforces.com/profile/rng_58)'s [word](https://codeforces.com/blog/entry/10690) if not mine.


## Problem Description

The best way to describe this problem is to visualise a bulletin board.
The board has a set of pins placed on the board and a loose piece of thread that starts from a hole, is placed in a certain manner and then ends at another hole.

So we have to predict the final state of the thread when it is pulled from these 2 holes resulting in a taut thread that loops around certain pins depending on its initial state.

Some examples of the initial state and final state are shown below :

![problem pic](/tightenup1s.png) ![problem pic](/tightenup4s.png)

Example 1, This shows the initial thread in an inverted s shape and how it loops around certain pins when pulled.

![problem pic](/tightenup2s.png) ![problem pic](/tightenup5s.png)

Example 2, This one forms a star.

![problem pic](/tightenup3s.png) ![problem pic](/tightenup6s.png)

Example 3, This one would have been really hard to simulate mentally.

.

So now that we've understood the problem statement its time to deal with the input, output specifics.
According to the [problem](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=1164) statement we're given multiple datasets, each dataset is preceded by a line containing m and n. 

Here m represents the number of vertices of the thread including starting and ending point, n represents the number of pins.
For each dataset there are m+n lines depicting x,y positions of m vertices of the thread and n positions of pins.
And we have to keep on reading datasets until we get 0 0 as an input.

A sample dataset is shown below :-

    m n
    x1 y1     -
    ...        | # m lines for thread vertices
    x1 y1     -
    x1 y1     -
    ...        | # n lines for pin positions
    x1 y1     -
    
    
In this case given below we are given 4 datasets :-

    m n     # 1st dataset
    x1 y1
    ...
    x1 y1
    m2 n2   # 2nd dataset
    x2 y2
    ...
    x2 y2
    m3 n3   # 3rd dataset
    x3 y3
    ...
    x3 y3
    m4 n4   # 4th dataset
    x4 y4
    ...
    x4 y4
    0 0     # end

As it can be seen we read the first line with m n; then read another m lines for the vertices of the thread and read another n lines for the positions of pins.  We keep on accepting datasets until we get 0,0. 

So for this case we read 4 datasets.

For the output we are supposed to return the length of the final state of the thread for each dataset, ie :-

    len1
    len2
    len3
    len4

Now that we've covered the semantics of the problem statement lets see how we can solve it.

`I would really recommend going through the`  [problem statement](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=1164) ` and thinking of solutions on your own for a few minutes as it'll make this post that much more easy to understand.`


## Solving the hardest problem on codeforces

Now that i had gone through the description a few times, i started thinking of ways to solve this problem.

### My Approach

A set of pins on x,y plane and a set of vertices that depict the thread. One thing thats not mentioned explicitly is that the order of the vertices of the thread are how it is being represented (like the thread goes from 1st point to 2nd point to 3rd point etc) otherwise there are innumerable ways to make a thread if you're just given an unordered set of vertices. 

#### Pulling on a loose thread

So if you imagine pulling on a loose thread that goes through some points you'll recollect that as it keeps on getting shorter, its vertices keep on decreasing until youre left with just the starting and ending point from where you were pulling.

![pull](/pulloutput.gif)

As it can be seen in the example above if there are no obstacles (pins) in the path of the thread its vertices and length will keep on decreasing. These 2 aspects are the key insights that can be taken from the above example. So we have to do the following transformation :-
- decrease the number of thread vertices
- decrease the length of the thread

Since both of these variables are correlated, as removing a vertex would also reduce the length, we have to decide dealing with which would be easier to do in an iterative manner, I went with removing the vertices of the thread to get to the final state.


#### Traversing through the thread

Now that we know that we'll be decreasing the number of vertices let's see how to traverse through the thread. To traverse through an ordered set of vertices we'll pick n vertices at a time and try to reduce them. 
So how do we decide on our n vertices. Lets see some examples


##### N vertices ~ n sided polygon

Lets say we take n vertices at a time and reduce them to their starting and ending vertex. 
Then taking a large n would help as in a single iteration we'll reduce n vertices.

![npoly](/npoly.gif)




But this is only valid if there are no pins in the n vertex polygon.

![npolygon](/npolygon.png)

If there are pins in the n vertex polygon. We could reduce the n sided poygon to starting vertex, pin and ending vertex, which involves doing the following transformation Xn->3n.

![npolygonpin](/npolygonpin.png)

But doing this would be wrong, as we would be assuming that the thread would definitely loop around the pin. If there's a case where in the final configuration the thread does not loop around this pin we would have no way of knowing how the thread loops around the pin and whether it can be removed like a vertex or not.

The example given below demonstrates how this transformation would result in an incorrect state:-

![incorrectstate1](/incorrectstate.gif)

Here we can see why we cant reduce vertices to pin positions prematurely, cause if we do go forward with the transformation we'll have to make sure that theres a way of knowing which side the thread occupies when going around the pin. Like in the case given below :-

![incorrectstate2](/pinloop1.png)                                                                                     

If we loop the thread with every pin that lies inside its n vertex polygon we get the following state if we just replace the vertex position with pin position for the thread representation:-

![incorrectstate3](/pinloop2.png)

Here if we loop the thread around the pin at the bottom we would not be able to reach the correct final state shown below :-

![incorrectstate4](/pinloop3.png)

We could also work around this by storing a tangent or a point to show where the thread has looped to resolve such cases as shown below :-

![incorrectstate5](/pinloop4.png)

But this doesnt seem to be too optimal as we'll have to calculate and store additional information about the tangent or point so lets see if theres another way.

So now that we have ruled out prematurely looping around pins, we still have to decide how to traverse through the thread.

Since we're not touching the pins anymore at this stage lets see which n gives us a better intermediate state.

![6Nvs3N](/6n3n.gif)

Here we can see that taking a small n is better as we get to a better intermediate state (with lesser length and lesser vertices) without iterating to an incorrect transformation (like the case of looping around pins prematurely).

Another big reason is that dealing with triangles is a lot more easier than n>3 polygons when it comes to applying functions like checking if a point lies inside the polygon. And we cant go lower than 3 vertices as that would just be a line.


#### Iterating through triangles

Now that we've decided to go through the thread 3 points at a time, lets see how that would pan out in a much more realistic scenario :-


![tightenuptraverse1](/tightenuptraverse1.png)

Here pins are depicted in blue and (Xn,Yn) represent pin positions, similiarly thread state and thread vertices are depicted in brown and (In,Jn) represent thread vertex positions.

Lets see how to tackle the first 3 thread vertices.

![triangleoutputgif](/triangleoutput.gif)

As it can be seen in this example if there is no point inside our triangle we remove the 2nd vertex according to the thread sequence in the 3 vertices. 

So the sequence of steps are as follows :-
- Go through the vertices 3 at a time
- Check if any pin lies inside the triangle
- If a pin lies inside the triangle we don't do anything and move 1 vertex ahead.
- If there is no pin inside the triangle we remove the 2nd vertex from the thread stack.
- Then we consider the following 3 vertices : vertex 1, vertex 3, new vertex after vertex 3. (after removing vertex 2)
- And then repeat these steps again

If we apply this sequence of steps for the whole thread this is what we get :-

![fulltraversalbig](/fulltraversalbig.gif)

Seems pretty neat doesn't it ?

Lets see compare our initial state and final state after going through the thread once.

![tightenuptraverse1x64](/tightenuptraverse1x64.png)

So our triangle traversal strategy seems to work nicely but there's still room for more reductions.

See if you can spot more triangles without pins in our final state.

![tightenuptraverse64xtri](/tightenuptraverse64xtri.png)

So we found 2 more triangles without pins that can be reduced.

The question now is whether there is a way of knowing beforehand how many times we'll have to go through the thread vertices. 

If there isn't we'll have to rely on traversing through the thread again until all the triangles without pins have been reduced.

So we'll keep on traversing through the thread until all triangles left have pins inside them. 

Again there are 2 strategies that can be used :-
1. Go through through the thread again using the same idea until we can't reduce triangles anymore.
2. Go through only the new triangles that were created by removing the vertices.

**For strategy 1 :** This is easier to implement as we can keep on updating a set which contains triangles that have pins, so we dont have to apply the functions on the ones for which we have checked in a previous iteration and only apply them when we come across a new triangle.

**For strategy 2 :** Here we only traverse through new triangles which were created after removing a vertex. So we create another array and add 2 triangles corresponding to every vertex removed.

Since strategy 2 seems more optimal lets see how that would work.

To see how to implement strategy 2 we consider a set of points p1 p2 p3 p4 p5, this would give us triangle T1 (p123), T2(p234), T3(p345).

So when we are traversing through the points, we reduce triangle T2(p234) by removing p3 which leaves us with p1 p2 p4 p5 . So after reducing T2 we get 2 new triangles T1(p124), T2(p245) that we have not considered before as shown in the example below :

![iteration2](/it2.gif)

So we would have to check for 2 new triangles T1(p124), T2(p245) for our next iteration to check if they can be reduced.

So the transformation can be simplified to the following step whenever a vertex is removed :-

![iteration2final](/it14.png)

i.e add 2 triangles to an array whenever a point is removed. Then we go through this second array and reduce more triangles, and repeat these steps until we are left with no more triangles that can be reduced.

 
#### Grouping pins according to triangles

One thing we have not discussed so far is how we are going to go through the pins when we check if they lie inside the triangle, for the thread it was straightforward as we had a starting point and ending point but here its not.

One way is we go through all the pins whenever we have to check if a pin lies inside a triangle.

Lets see how that would work, we have ` m ` thread vertices. But since we are going through them 3 at a time we would have  ` m-2 ` triangles that have to be checked for ` n ` pins. 

i.e ` n * (m-2) ` times we would have to check for pins, in every iteration which is a lot. Lets see if we can reduce it.

So can we group pins according to their corresponding triangles. 

This can be done if we take the lowest x,y points and highest x,y points out of the 3 points and check which pins lie in that range.

We need a fast look up method to access pins in a given x and y range.
bsp, kd tree+, quad tree



#### Reducing the thread

 



##



## Philosophical end quote

Problems are meant to be conquered dont let any red flag holding, low acceptance rate bearing, problem ever convince you to believe otherwise.


> **P.S : I tried my best to reduce chunks of text by adding accompanying visual explanations and reducing the text block size by being more precise, i hope the explanations are a medium or easy read, ciao.**