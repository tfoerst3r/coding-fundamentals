---
title: Matplotlib
---

<h1> Matplotlib - create your plots </h1>

Examples are based on [this course material provided by HIFIS](https://hifis.net/workshop-materials/python-matplotlib/).
Matplotlib bases there setup strategy as **imperative**.


## Getting started

### Order of declaring 

Matplotlib is sensitive to the order of which you declare elements.

1. Data
2. Decoration (labels, title)
3. Plot elements from back to front (lines)
4. Plot area scaling, transformations 

Alternatively use the 'zorder' for declaration purposes


## One plot - single line

``` python
#== IMPORTS ==#
from matplotlib import pyplot
import numpy

#== FUNCTIONS ==#

def myrange(start,stop,step):
    x0=start; dx=step; x1=stop+step
    return numpy.arange(x0,x1,dx)

#== CONSTANTS ==#

#== VARIABLES ==#
months = [
    "Jan", "Feb", "Mar", "Apr", "May", "Jun",
    "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"
]

water_levels_2010 = [
    5.77, 6.04, 6.52, 6.48, 6.54, 5.92,
    5.64, 5.21, 5.01, 5.18, 5.45, 5.59
]

ytics0=4
ytics1=8
dy=0.5

#== ROUTINE ==#
#-- Data to plot
pyplot.plot(months,water_levels_2010)

#-- Configure plot parameters
pyplot.xlabel("Month")
pyplot.ylabel("[m]")
pyplot.title("Avg. water level of a river in 2022")

#-- Lines
marker_xlines = [min(water_levels_2010), max(water_levels_2010)]
pyplot.hlines(
    y=marker_xlines,
    xmin=months[0],
    xmax=months[-1],
    linestyles="dotted",
    colors="lightgray"
)

#-- Tics
ytics_water_level = myrange(ytics0,ytics1,dy)
pyplot.yticks(ytics_water_level)

#-- Plotting
pyplot.show()
```


## One plot - multi line 

``` python
#== IMPORTS ==#
from matplotlib import pyplot
import numpy

#== FUNCTIONS ==#

def myrange(start,stop,step):
    x0=start; dx=step; x1=stop+step
    return numpy.arange(x0,x1,dx)

#== CONSTANTS ==#

#== VARIABLES ==#
months = [
    "Jan", "Feb", "Mar", "Apr", "May", "Jun",
    "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"
]

water_levels_2010 = [
    5.77, 6.04, 6.52, 6.48, 6.54, 5.92, 
    5.64, 5.21, 5.01, 5.18, 5.45, 5.59
]

water_levels_2020 = [
    5.48, 5.82, 6.31, 6.26, 6.09, 5.87, 
    5.72, 5.54, 5.22, 4.86, 5.12, 5.40
]

ytics0=4
ytics1=8
dy=0.5

#== ROUTINE ==#
#-- Data to plot
pyplot.plot(months,water_levels_2010, label="2010")
pyplot.plot(months,water_levels_2020, label="2020")

#-- Configure plot parameters
pyplot.xlabel("Month")
pyplot.ylabel("[m]")
pyplot.title("Avg. water level of a river in 2022")
pyplot.legend()

#-- Lines
marker_xlines = [min(water_levels_2010 + water_levels_2020), max(water_levels_2010 + water_levels_2020)]
pyplot.hlines(
    y=marker_xlines,
    xmin=months[0],
    xmax=months[-1],
    linestyles="dotted",
    colors="lightgray"
)

#-- Tics
ytics_water_level = myrange(ytics0,ytics1,dy)
pyplot.yticks(ytics_water_level)

#-- Plotting
pyplot.show()
```

<!------------------->
<!----- SECTION ----->
<!------------------->
## Multi plot - imperative

```python
#== IMPORTS ==#
from matplotlib import pyplot
import numpy

#== FUNCTIONS ==#

def myrange(start,stop,step):
    """
    Generate a range list of start stop step intervals
    """
    x0=start;dx=0.5;x1=8+step
    return numpy.arange(x0,x1,dx)


# Calculate the difference
def diffdata(data1,data0):
    """
    Difference fron dataset 1 to dataset 2
    """
    diff = [ data1[idx] - data0[idx] for idx in range(len(data1)) ] 
    return diff



#== CONSTANTS ==#

#== VARIABLES ==#
months = [
    "Jan", "Feb", "Mar", "Apr", "May", "Jun",
    "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"
]

water_levels_2010 = [
    5.77, 6.04, 6.52, 6.48, 6.54, 5.92, 
    5.64, 5.21, 5.01, 5.18, 5.45, 5.59
]

water_levels_2020 = [
    5.48, 5.82, 6.31, 6.26, 6.09, 5.87, 
    5.72, 5.54, 5.22, 4.86, 5.12, 5.40
]

ytics0=4
ytics1=8
dy=0.5

#== ROUTINE ==#
#-- Overall plot structure
figure, axes = pyplot.subplots(nrows=2, ncols=2) # (2)
#figure, axes = pyplot.subplots(nrows=1, ncols=2) # (2)



#-- Plot 1
#pyplot.sca(axes[0])
pyplot.sca(axes[0][0])
#-- Data
pyplot.plot(months,water_levels_2010, label="2010")
pyplot.plot(months,water_levels_2020, label="2020")

#-- Configure plot parameters
pyplot.title("Avg. water level of a river in 2010 and 2020")
pyplot.xlabel("Month")
pyplot.ylabel("[m]")
pyplot.legend()

#-- Lines
marker_xlines = [min(water_levels_2010 + water_levels_2020), max(water_levels_2010 + water_levels_2020)]
pyplot.hlines(
    y=marker_xlines,
    xmin=months[0],
    xmax=months[-1],
    linestyles="dotted",
    colors="lightgray"
)

#-- Tics
ytics_water_level = myrange(ytics0,ytics1,dy)
pyplot.yticks(ytics_water_level)


#-- Plot 2
#pyplot.sca(axes[0])
pyplot.sca(axes[1][1])

#-- Data
water_level_differences = diffdata(water_levels_2020, water_levels_2010)
pyplot.plot(months, water_level_differences)

#-- Configure plot parameters
pyplot.title("Difference between water levels")
pyplot.xlabel("Month")
pyplot.ylabel("[m]")

#-- Lines
pyplot.hlines(0, xmin=months[0], xmax=months[-1], color="black")

#--------------#
#-- PLOTTING --#
#.. setting padding
figure.tight_layout(pad=0.5)
#pyplot.show()
#--------------#
```


## Multi plot - object oriented


``` python
#== IMPORTS ==#
from matplotlib import pyplot
import numpy

#== FUNCTIONS ==#

def myrange(start,stop,step):
    """
    Generate a range list of start stop step intervals
    """
    x0=start;dx=0.5;x1=8+step
    return numpy.arange(x0,x1,dx)


# Calculate the difference
def diffdata(data1,data0):
    """
    Difference fron dataset 1 to dataset 2
    """
    diff = [ data1[idx] - data0[idx] for idx in range(len(data1)) ] 
    return diff



#== CONSTANTS ==#

#== VARIABLES ==#
months = [
    "Jan", "Feb", "Mar", "Apr", "May", "Jun",
    "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"
]

water_levels_2010 = [
    5.77, 6.04, 6.52, 6.48, 6.54, 5.92, 
    5.64, 5.21, 5.01, 5.18, 5.45, 5.59
]

water_levels_2020 = [
    5.48, 5.82, 6.31, 6.26, 6.09, 5.87, 
    5.72, 5.54, 5.22, 4.86, 5.12, 5.40
]

ytics0=4
ytics1=8
dy=0.5

#== ROUTINE ==#

#-- Create a figure object
myfigure = pyplot.figure()

#-- Create the subplot layout (axes)
axes = myfigure.subplots(nrows=2, ncols=2)

#-- Alternative unfolding practices
# https://stackoverflow.com/questions/43205928/how-does-unpacking-in-fig-ax-plt-subplots-work-for-more-than-one-subplot
# ((plotA,b),(c,plotB)) = myfigure.subplots(nrows=2, ncols=2)

#---------------------------------------------------------
#-- Plot 1
#-- Data
axes[0][0].plot(months,water_levels_2010, label="2010")
axes[0][0].plot(months,water_levels_2020, label="2020")

#-- Configure plot parameters
axes[0][0].set_title("Avg. water level of a river in 2010 and 2020")
axes[0][0].set_xlabel("Month")
axes[0][0].set_ylabel("[m]")
axes[0][0].legend()

#-- Lines
marker_xlines = [min(water_levels_2010 + water_levels_2020), max(water_levels_2010 + water_levels_2020)]
axes[0][0].hlines(
    y=marker_xlines,
    xmin=months[0],
    xmax=months[-1],
    linestyles="dotted",
    colors="lightgray"
)

#-- Tics
axes[0][0].set_yticks(myrange(ytics0,ytics1,dy))


#---------------------------------------------------------
#-- Plot 2
pyplot.sca(axes[1][1])

#-- Data
axes[1][1].plot(months, diffdata(water_levels_2020, water_levels_2010))
 
#-- Configure plot parameters
axes[1][1].set_title("Water levels 2020 compared to 2010")
axes[1][1].set_xlabel("Month")
axes[1][1].set_ylabel("[m]")
 
 #-- Lines
axes[1][1].hlines(0, xmin=months[0], xmax=months[-1], color="black")
  

#---------------------------------------------------------
#-- PLOTTING --#
#.. setting padding
myfigure.tight_layout(pad=0.5)
myfigure.show()
```


## Matplot and Pandas


``` python 
#== IMPORTS ==#
from matplotlib import pyplot
from pandas import Series, DataFrame
import numpy

#== FUNCTIONS ==#

def myrange(start,stop,step):
    """
    Generate a range list of start stop step intervals
    """
    x0=start;dx=0.5;x1=8+step
    return numpy.arange(x0,x1,dx)


# Calculate the difference
def diffdata(data1,data0):
    """
    Difference fron dataset 1 to dataset 2
    """
    diff = [ data1[idx] - data0[idx] for idx in range(len(data1)) ] 
    return diff



#== CONSTANTS ==#

#== VARIABLES ==#

xname = 'Months'
months = { xname:
    ["Jan", "Feb", "Mar", "Apr", "May", "Jun",
    "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"]
}


water_levels = { 
    "2010": [5.77, 6.04, 6.52, 6.48, 6.54, 5.92, 
    5.64, 5.21, 5.01, 5.18, 5.45, 5.59],
    "2020": [5.48, 5.82, 6.31, 6.26, 6.09, 5.87, 
    5.72, 5.54, 5.22, 4.86, 5.12, 5.40]
}

collection = water_levels | months

ytics0=4
ytics1=8
dy=0.5

#== ROUTINE ==#

#-- Create pandas series
#df = DataFrame(
#    data={
#        'Months': months,
#        "Level 2020": water_levels_2020,
#        "Level 2010": water_levels_2010,
#    }
#).set_index('Months')

df = DataFrame(
    data=collection
).set_index(xname)


#df = DataFrame(
#    data={
#        "2020": water_levels_2020,
#        "2010": water_levels_2010,
#    },
#    index = months
#)

#-- Create a figure object
myfigure = pyplot.figure()

#-- Create the subplot layout (axes)
axes = myfigure.subplots(nrows=2, ncols=2)

#---------------------------------------------------------
#-- Plot 1
#-- Data
axes[0][0].plot(df, label=df.keys())

#-- Configure plot parameters
axes[0][0].set_title("Avg. water level of a river in 2010 and 2020")
axes[0][0].set_xlabel("Month")
axes[0][0].set_ylabel("[m]")
axes[0][0].legend()

#-- Lines
marker_xlines = [
    min(df["2020"].append(df["2010"])),
    max(df["2020"].append(df["2010"]))
]

axes[0][0].hlines(
    y=marker_xlines,
    xmin=df.index.values[0],
    xmax=df.index.values[-1],
    linestyles="dotted",
    colors="lightgray"
)

#-- Tics
axes[0][0].set_yticks(myrange(ytics0,ytics1,dy))


#---------------------------------------------------------
#-- Plot 2
pyplot.sca(axes[1][1])

#-- Data
axes[1][1].plot(df.index.values, diffdata(df["2020"], df["2010"]))
 
#-- Configure plot parameters
axes[1][1].set_title("Water levels 2020 compared to 2010")
axes[1][1].set_xlabel("Month")
axes[1][1].set_ylabel("[m]")
 
 #-- Lines
axes[1][1].hlines(0, xmin=df.index.values[0], xmax=df.index.values[-1], color="black")
  

#---------------------------------------------------------
#-- PLOTTING --#
#.. setting padding
myfigure.tight_layout(pad=0.5)
#.. create the plot
myfigure.show()
```


