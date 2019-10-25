---
title: Data Manipulation with R
layout: post
---

In this blog, we'll learn about the R programming language by using it to manipulate a dataset

The dataset we'll use is available on [Kaggle](https://www.kaggle.com/) and contains data about forest fires in Brazil, [click here to check it out](https://www.kaggle.com/gustavomodelli/forest-fires-in-brazil)

Now that we have our dataset ready, we need to import our libraries, for this example we'll use dplyr (for general data manipulation tools) and ggplot2 (for plotting capabilities)

```R
library(dplyr)
library(ggplot2)
```

Then, after downloading and extracting our dataset, we need to import it into our R program, like so:

```R
path = "R:\\R Projects\\" # Change this to the path in your machine
data = read.csv(paste(path,"amazon.csv",sep=""))
```

So now we can take a look at our data:

```R
str(data)
```
>
    'data.frame':	6454 obs. of  5 variables:
     $ year  : int  1998 1999 2000 2001 2002 2003 2004 2005 2006 2007 ...
     $ state : Factor w/ 23 levels "Acre","Alagoas",..: 1 1 1 1 1 1 1 1 1 1 ...
     $ month : Factor w/ 12 levels "Abril","Agosto",..: 5 5 5 5 5 5 5 5 5 5 ...
     $ number: num  0 0 0 0 0 10 0 12 4 0 ...
     $ date  : Factor w/ 20 levels "1998-01-01","1999-01-01",..: 1 2 3 4 5 6 7 8 9 10 ...
    

As you can see above, the year variable was read as integer, but we need it as a Factor variable, so we'll execute:


```R
data$year <- factor(data$year)
```

Now let's check our data again


```R
str(data)
```
>
    'data.frame':	6454 obs. of  5 variables:
     $ year  : Factor w/ 20 levels "1998","1999",..: 1 2 3 4 5 6 7 8 9 10 ...
     $ state : Factor w/ 23 levels "Acre","Alagoas",..: 1 1 1 1 1 1 1 1 1 1 ...
     $ month : Factor w/ 12 levels "Abril","Agosto",..: 5 5 5 5 5 5 5 5 5 5 ...
     $ number: num  0 0 0 0 0 10 0 12 4 0 ...
     $ date  : Factor w/ 20 levels "1998-01-01","1999-01-01",..: 1 2 3 4 5 6 7 8 9 10 ...
    

That's interesting and all, but if we want to have a better idea about the numerical variables and group sizes of categorical variables, we can use the following command:


```R
summary(data)
```


          year              state            month          number     
     1998   : 324   Rio        : 717   Janeiro  : 541   Min.   :  0.0  
     1999   : 324   Mato Grosso: 478   Abril    : 540   1st Qu.:  3.0  
     2000   : 324   Paraiba    : 478   Agosto   : 540   Median : 24.0  
     2001   : 324   Alagoas    : 240   Fevereiro: 540   Mean   :108.3  
     2002   : 324   Acre       : 239   Julho    : 540   3rd Qu.:113.0  
     2003   : 324   Amapa      : 239   Junho    : 540   Max.   :998.0  
     (Other):4510   (Other)    :4063   (Other)  :3213                  
             date     
     1998-01-01: 324  
     1999-01-01: 324  
     2000-01-01: 324  
     2001-01-01: 324  
     2002-01-01: 324  
     2003-01-01: 324  
     (Other)   :4510  


We can check how many instances of each value for a specific variable there are by executing:


```R
table(data$month)
```


>    
        Abril    Agosto  Dezembro Fevereiro   Janeiro     Julho     Junho      Maio 
          540       540       513       540       541       540       540       540 
        Março  Novembro   Outubro  Setembro 
          540       540       540       540 


Now let's filter our data and only get data from the month of november


```R
november_data <- filter(data, month == "Novembro")
```

Let's check the original data again to make sure it's unchanged


```R
str(data)
```
>
    'data.frame':	6454 obs. of  5 variables:
     $ year  : Factor w/ 20 levels "1998","1999",..: 1 2 3 4 5 6 7 8 9 10 ...
     $ state : Factor w/ 23 levels "Acre","Alagoas",..: 1 1 1 1 1 1 1 1 1 1 ...
     $ month : Factor w/ 12 levels "Abril","Agosto",..: 5 5 5 5 5 5 5 5 5 5 ...
     $ number: num  0 0 0 0 0 10 0 12 4 0 ...
     $ date  : Factor w/ 20 levels "1998-01-01","1999-01-01",..: 1 2 3 4 5 6 7 8 9 10 ...
    

And now we can take a look at our new data and make sure the filtering was done correctly


```R
str(november_data)
```
>
    'data.frame':	540 obs. of  5 variables:
     $ year  : Factor w/ 20 levels "1998","1999",..: 1 2 3 4 5 6 7 8 9 10 ...
     $ state : Factor w/ 23 levels "Acre","Alagoas",..: 1 1 1 1 1 1 1 1 1 1 ...
     $ month : Factor w/ 12 levels "Abril","Agosto",..: 10 10 10 10 10 10 10 10 10 10 ...
     $ number: num  0 0 0 1 86 15 10 14 18 7 ...
     $ date  : Factor w/ 20 levels "1998-01-01","1999-01-01",..: 1 2 3 4 5 6 7 8 9 10 ...
    


```R
table(november_data$month)
```


>    
        Abril    Agosto  Dezembro Fevereiro   Janeiro     Julho     Junho      Maio 
            0         0         0         0         0         0         0         0 
        Março  Novembro   Outubro  Setembro 
            0       540         0         0 



```R
str(november_data$month)
```
>
     Factor w/ 12 levels "Abril","Agosto",..: 10 10 10 10 10 10 10 10 10 10 ...
    

Above we can see that although we removed all instances where the month was not november, all the other months are still options in the month Factor. To remove the empty options in the Factor, we can use the following command:


```R
november_data$month <- droplevels(november_data$month)
```

Now we can check it, and see that the months that had zero instances in the Factor were removed, and we have a Factor with only 1 level, by running the following commands:


```R
str(november_data$month)
```
>
     Factor w/ 1 level "Novembro": 1 1 1 1 1 1 1 1 1 1 ...
    


```R
table(november_data$month)
```


>    
    Novembro 
         540 


Next we'll see how the pipe operator works, for that we'll filter another segment of our data, from the month of october this time


```R
str(data)
```
>
    'data.frame':	6454 obs. of  5 variables:
     $ year  : Factor w/ 20 levels "1998","1999",..: 1 2 3 4 5 6 7 8 9 10 ...
     $ state : Factor w/ 23 levels "Acre","Alagoas",..: 1 1 1 1 1 1 1 1 1 1 ...
     $ month : Factor w/ 12 levels "Abril","Agosto",..: 5 5 5 5 5 5 5 5 5 5 ...
     $ number: num  0 0 0 0 0 10 0 12 4 0 ...
     $ date  : Factor w/ 20 levels "1998-01-01","1999-01-01",..: 1 2 3 4 5 6 7 8 9 10 ...
    


```R
october_data <- data %>% filter(month == "Outubro")
```

As can be seen above, the pipe operator takes its first operand and sets it as the first argument of the function which is its second operand


```R
table(october_data$month)
```


>    
        Abril    Agosto  Dezembro Fevereiro   Janeiro     Julho     Junho      Maio 
            0         0         0         0         0         0         0         0 
        Março  Novembro   Outubro  Setembro 
            0         0       540         0 


Above we can see that the result of the operation was similar to when we executed `november_data <- filter(data, month == "Novembro")`

Now let's plot our data (we're using the whole dataset now), here's the code we'll use:


```R
plot <- ggplot(data = data, aes(x = year, y = number, fill = month)) + geom_col()
plot + theme(axis.text.x = element_text(angle=90))
```

> ![png](/Blog-Resume/assets/images/data_manip_forest_fire_graph.png)