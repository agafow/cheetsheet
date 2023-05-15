
Ploting basics

```R
#define the cars  vector with 5 elements
cars = c(1,3,6,4,9)

#Graph the cars vector with all defaults 
plot(cars)

```
The out is here

![[R01.png]]

```R
# Graph cars using blue points overlayed by a line "o" is ow not zero 
plot(cars, type="o", col="blue")

# Create a title with a red, bold/italic font
title(main="Autos", col.main="red", font.main=4)

# Define 2 vectors
cars = c(1,2,6,4,9)
trucks = c(2,5,4,5,12)

# Graph cars using a y axis that ranges from 0 to 12
plot(cars, type="o", col ="blue", ylim = c(0,12))

# Graph trucks with red dashed line and square points
lines(trucks, type="o", pch=22, lty=2, col="red")

# Create a title with a red, bold/italic font
title(main="Autos", col.main="red", font.main=4)

#The out is follows
```

![[Rplot03.png]]




Resourses links

[Rplots](https://sites.harding.edu/fmccown/r/)


