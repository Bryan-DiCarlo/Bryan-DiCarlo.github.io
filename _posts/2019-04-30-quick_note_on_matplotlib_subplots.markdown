---
layout: post
title:      "Quick Note on Matplotlib Subplots"
date:       2019-04-30 19:32:31 -0400
permalink:  quick_note_on_matplotlib_subplots
---


The first time I saw or tried to create a canvas of subplots in Matplotlib, well let’s just say it was not readily intuitive for me.  Below I try to document a quick and easy recipe to get started displaying multiple visualizations in one output cell or on one “canvas”.  In this case I will be creating four side by side graphs. Or perhaps more descriptively one row with four columns of graphs. This is a rough functional method that will produce the desired results.  It was useful for me when first getting started trying to produce visualizations on my own.  It helped me understand conceptually what was taking place so I could at least move forward and produce the desired visualizations.  It certainly does not do justice to what Matplotlib is capable of. For that you should consult the documentation page at [Matplotlib](http://matplotlib.org).

### First create the main canvas where the subplots will be displayed


```
plt.figure(figsize=(18,8), dpi=200)

```

* figsize(width, height (in)) we are producing four figs on one row so it needs to be wide enough to fit all four

### Second, we will designate our four subplots


```
plt.figure(figsize=(18,8), dpi=200)

ax1 = plt.subplot(1,4,1)
ax2 = plt.subplot(1,4,2)
ax3 = plt.subplot(1,4,3)
ax4 = plt.subplot(1,4,4)

```


* By convention you are telling Matplotlib where to place each figure on the canvas
* (1,4,1) Represents 1 row, 4 columns, 1st figure
* (1,4,2) Represents 1 row, 4 columns, 2nd figure
* (1,4,3) Represents 1 row, 4 columns, 3rd figure
* (1,4,4) Represents 1 row, 4 columns, 4th figure


### Lastly create each labeled subplot as you would for an individual plot

```
plt.figure(figsize=(18,8), dpi=200)

ax1 = plt.subplot(1,4,1)
ax2 = plt.subplot(1,4,2)
ax3 = plt.subplot(1,4,3)
ax4 = plt.subplot(1,4,4)

ax1.scatter(df1['sqft_living'], df1['price']) 
ax1.set_title('Sqft_living vs Price')
ax1.set_ylabel('PRICE')
ax1.set_xlabel('Sqft_Living')

ax2.scatter(df1['grade'], df1['price']) 
ax2.set_title('Grade vs Price')
ax2.set_xlabel('Grade')

ax3.scatter(df1['sqft_above'], df1['price']) 
ax3.set_title('Sqft_Above vs Price')
ax3.set_xlabel('Sqft_Above')

ax4.scatter(df1['sqft_living15'], df1['price']) 
ax4.set_title('Sqft_Living15 vs Price')
ax4.set_xlabel('Sqft_Living15')

plt.tight_layout()
```




This will produce four subplots side by side.  Hope that helps.  This was useful for me to have it broken down this way when I was first getting started.  There is much more that Matplotlib is capable of and further refinements to this method you can make.  This is just scratching the surface.  I encourage you to visit the documentation page linked above.








