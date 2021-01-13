---
title: "Homework 2"
author: "Aryss Hearne | 915651636"
date: "1/9/2021"
output: 
  html_document: 
    keep_md: yes
---



##### 1. What is a vector in R? 

*A vector is an organizational tool for different types of data within R. These data include characters, integers, and numeric data.*

##### 2. WHat is a data matrix in R?

*Data matrices are ways of concatenating large pieces of data. They stack vectors similar to data tables.*

##### 3. Below are data collected by three scientists (Jill, Steve, Susan in order) measuring temperatures of eight hot springs. Run this code chunk to create the vectors.


```r
spring_1 <- c(36.25, 35.40, 35.30)
spring_2 <- c(35.15, 35.35, 33.35)
spring_3 <- c(30.70, 29.65, 29.20)
spring_4 <- c(39.70, 40.05, 38.65)
spring_5 <- c(31.85, 31.40, 29.30)
spring_6 <- c(30.20, 30.65, 29.75)
spring_7 <- c(32.90, 32.50, 32.80)
spring_8 <- c(36.80, 36.45, 33.15)
```

##### 4. Build a data matrix that has the springs as rows and the columns as scientists.


```r
Springn <- c(spring_1,spring_2,spring_3, spring_4, spring_5, spring_6, spring_7, spring_8)
spring_matrix <- matrix(Springn, nrow=8, byrow=T)
spring_matrix
```

```
##       [,1]  [,2]  [,3]
## [1,] 36.25 35.40 35.30
## [2,] 35.15 35.35 33.35
## [3,] 30.70 29.65 29.20
## [4,] 39.70 40.05 38.65
## [5,] 31.85 31.40 29.30
## [6,] 30.20 30.65 29.75
## [7,] 32.90 32.50 32.80
## [8,] 36.80 36.45 33.15
```

##### 5. The names of the springs are 1.Bluebell Spring, 2.Opal Spring, 3.Riverside Spring, 4.Too Hot Spring, 5.Mystery Spring, 6.Emerald Spring, 7.Black Spring, 8.Pearl Spring. Name the rows and columns in the data matrix. Start by making two new vectors with the names, then use `colnames()` and `rownames()` to name the columns and rows.


```r
scientist <- c("Jill", "Steve", "Susan")
Springs <- c("Bluebell Spring","Opal Spring","Riverside Spring","Too Hot Spring", "Mystery Spring", "Emerald Spring", "Black Spring", "Pearl Spring")
colnames(spring_matrix) <- scientist
rownames(spring_matrix) <- Springs
spring_matrix
```

```
##                   Jill Steve Susan
## Bluebell Spring  36.25 35.40 35.30
## Opal Spring      35.15 35.35 33.35
## Riverside Spring 30.70 29.65 29.20
## Too Hot Spring   39.70 40.05 38.65
## Mystery Spring   31.85 31.40 29.30
## Emerald Spring   30.20 30.65 29.75
## Black Spring     32.90 32.50 32.80
## Pearl Spring     36.80 36.45 33.15
```

##### 6. Calculate the mean temperature of all eight springs.


```r
Bluebell <- spring_matrix[1,]
Bluebell_Mean <- mean(Bluebell)
Opal <- spring_matrix[2,]
Opal_mean <- mean(Opal)
Riverside <- spring_matrix[3,]
Riverside_mean <- mean(Riverside)
Hot <- spring_matrix[4,]
Hot_mean <- mean(Hot)
Mystery <- spring_matrix[5,]
Mystery_mean <- mean(Mystery)
Emerald <- spring_matrix[6,]
Emerald_mean <- mean(Emerald)
Black <- spring_matrix[7,]
Black_mean <- mean(Black)
Pearl <- spring_matrix[8,]
Pearl_mean <- mean(Pearl)
Mean_Temp <- c(Bluebell_Mean,Opal_mean,Riverside_mean,Hot_mean,Mystery_mean,Emerald_mean, Black_mean, Pearl_mean)
Mean_Temp
```

```
## [1] 35.65000 34.61667 29.85000 39.46667 30.85000 30.20000 32.73333 35.46667
```
##### 7. Add this as a new column in the data matrix.


```r
spring_matrix <- cbind(spring_matrix,Mean_Temp)
spring_matrix
```

```
##                   Jill Steve Susan Mean_Temp
## Bluebell Spring  36.25 35.40 35.30  35.65000
## Opal Spring      35.15 35.35 33.35  34.61667
## Riverside Spring 30.70 29.65 29.20  29.85000
## Too Hot Spring   39.70 40.05 38.65  39.46667
## Mystery Spring   31.85 31.40 29.30  30.85000
## Emerald Spring   30.20 30.65 29.75  30.20000
## Black Spring     32.90 32.50 32.80  32.73333
## Pearl Spring     36.80 36.45 33.15  35.46667
```
##### 8. Show Susan’s value for Opal Spring only.


```r
Susan_Opal <- spring_matrix[2,3]
Na <- NA
SusanOpal <- c(Na,Na,Susan_Opal,Na)
spring_matrix <- rbind(spring_matrix,SusanOpal)
spring_matrix
```

```
##                   Jill Steve Susan Mean_Temp
## Bluebell Spring  36.25 35.40 35.30  35.65000
## Opal Spring      35.15 35.35 33.35  34.61667
## Riverside Spring 30.70 29.65 29.20  29.85000
## Too Hot Spring   39.70 40.05 38.65  39.46667
## Mystery Spring   31.85 31.40 29.30  30.85000
## Emerald Spring   30.20 30.65 29.75  30.20000
## Black Spring     32.90 32.50 32.80  32.73333
## Pearl Spring     36.80 36.45 33.15  35.46667
## SusanOpal           NA    NA 33.35        NA
```
##### 9. Calcualte the mean for Jill's column only


```r
Jillstemp <- spring_matrix[,1]
Jill_Mean_temp <- mean(Jillstemp, na.rm=T)
Mean_Temp_Jill <- c(Jill_Mean_temp, Na, Na, Na)
spring_matrix <- rbind(spring_matrix,Mean_Temp_Jill)
spring_matrix
```

```
##                      Jill Steve Susan Mean_Temp
## Bluebell Spring  36.25000 35.40 35.30  35.65000
## Opal Spring      35.15000 35.35 33.35  34.61667
## Riverside Spring 30.70000 29.65 29.20  29.85000
## Too Hot Spring   39.70000 40.05 38.65  39.46667
## Mystery Spring   31.85000 31.40 29.30  30.85000
## Emerald Spring   30.20000 30.65 29.75  30.20000
## Black Spring     32.90000 32.50 32.80  32.73333
## Pearl Spring     36.80000 36.45 33.15  35.46667
## SusanOpal              NA    NA 33.35        NA
## Mean_Temp_Jill   34.19375    NA    NA        NA
```

##### 10. Use the data matrix to perform one calculation or operation of your interest.


```r
G_T <- mean(Mean_Temp, na.rm=T)
Avg_temp <- c(Na, Na, Na, G_T)
spring_matrix <- rbind(spring_matrix, Avg_temp)
spring_matrix
```

```
##                      Jill Steve Susan Mean_Temp
## Bluebell Spring  36.25000 35.40 35.30  35.65000
## Opal Spring      35.15000 35.35 33.35  34.61667
## Riverside Spring 30.70000 29.65 29.20  29.85000
## Too Hot Spring   39.70000 40.05 38.65  39.46667
## Mystery Spring   31.85000 31.40 29.30  30.85000
## Emerald Spring   30.20000 30.65 29.75  30.20000
## Black Spring     32.90000 32.50 32.80  32.73333
## Pearl Spring     36.80000 36.45 33.15  35.46667
## SusanOpal              NA    NA 33.35        NA
## Mean_Temp_Jill   34.19375    NA    NA        NA
## Avg_temp               NA    NA    NA  33.60417
```
