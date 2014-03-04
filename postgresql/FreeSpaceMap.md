# The Free Space Map of PostgreSQL

## What for?
The most common operation needed for insert a tuple is to find a continuous space in the file. 
The arguement should be the size needed.

## simple approach
a flat array, scan from head to tail.

## using tree
The tree method is the direct improvement. However, we still have these questions.

+ the leaves, the internal nodes, the root
What should they stored? the depth?

+ the granularity of different sizes
an integer?

+ how to update these information?
each time once updated?

+ how to resolve the potential possession of the same block?
by random? or just ignore it?

+ how to transfer between the physical offset among the pages and logical pages(data page and internal meta page)
a large lookup table? an algebratic calculation?

## implementation

+ leaves and non-leaf node
A leaf contains a batch of free space sizes. The organization of these data is a max heap.
An internal node also contains an array. Each element is the maxium size of its sub-pages.
The tree has 3 levels.

+ granulairy of the size
Use a byte to represent the sizes. So you can only have these sizes: 0 * pagesize/256, 2 * pagesize/256, 3 * pagesize/256...
So we have **about** 8k * 8k * 8k pages in each table.

+ how to update these information?
By the size do not up-propragate too high. Base on the assumption, we do **NOT** guarantee the page has the size it has advocated.

The assumption has two reasons:
 + easy to maintain, the modification do not fan-out too much
 + the coarse + retry stragtagy is easy allows concurrent request
 + finally fixxed by gabage collection, which used the term "Vaccum"
 
+ concurrent request?
First, as descripted above, you may retry when not enough space as you expected.
Second, we record another number, indicating the search you should start on the heap. 

If the number of the page is not satisfied, search the parent until root.

The final search hehavior would be down to leaf , or up then down.

Two different searches would be like a scan from last point.

+ how to transfer between physical to logical?
You should remember that the depth is fixxed at 3.
Below is a simplify table, the left-most column is the physical page number. The right ones is the offset from each depth.
The assumption is each node has only **two** descendants.

0  0
1    0
2      0 
3      1
4    1
5      2
6      3
7 1
8    2
9      4
