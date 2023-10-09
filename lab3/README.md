# lab3

# Основы обработки данных с помощью R (часть 2)

## Цель работы

1.  Закрепить практические навыки использования языка программирования R
    для обработки данных

2.Закрепить знания основных функций обработки данных экосистемы
tidyverse языка R

1.  Развить пркатические навыки использования функций обработки данных
    пакета dplyr -- функции select(), filter(), mutate(), arrange(),
    group_by()

## Задание (Ход работы)

Проанализировать встроенные в пакет nycflights13 наборы данных с помощью
языка R и ответить на вопросы:

``` r
library(dplyr)
```


    Attaching package: 'dplyr'

    The following objects are masked from 'package:stats':

        filter, lag

    The following objects are masked from 'package:base':

        intersect, setdiff, setequal, union

``` r
library(nycflights13)
```

### 1. Сколько встроенных в пакет nycflights13 датафреймов?

nycflights13::

Ответ: 5 датафреймов (airlines, airports, flights, planes, weather)

### 2. Сколько строк в каждом датафрейме?

``` r
library('nycflights13')
library('dplyr')
nycflights13::airlines %>% nrow()
```

    [1] 16

``` r
nycflights13::airports %>% nrow()
```

    [1] 1458

``` r
nycflights13::flights %>% nrow()
```

    [1] 336776

``` r
nycflights13::planes %>% nrow()
```

    [1] 3322

``` r
nycflights13::weather %>% nrow()
```

    [1] 26115

### 3. Сколько столбцов в каждом датафрейме?

``` r
nycflights13::airlines %>% ncol()
```

    [1] 2

``` r
nycflights13::airports %>% ncol()
```

    [1] 8

``` r
nycflights13::flights %>% ncol()
```

    [1] 19

``` r
nycflights13::planes %>% ncol()
```

    [1] 9

``` r
nycflights13::weather %>% ncol()
```

    [1] 15

### 4. Как просмотреть примерный вид датафрейма?

``` r
nycflights13::airports %>% glimpse()
```

    Rows: 1,458
    Columns: 8
    $ faa   <chr> "04G", "06A", "06C", "06N", "09J", "0A9", "0G6", "0G7", "0P2", "…
    $ name  <chr> "Lansdowne Airport", "Moton Field Municipal Airport", "Schaumbur…
    $ lat   <dbl> 41.13047, 32.46057, 41.98934, 41.43191, 31.07447, 36.37122, 41.4…
    $ lon   <dbl> -80.61958, -85.68003, -88.10124, -74.39156, -81.42778, -82.17342…
    $ alt   <dbl> 1044, 264, 801, 523, 11, 1593, 730, 492, 1000, 108, 409, 875, 10…
    $ tz    <dbl> -5, -6, -6, -5, -5, -5, -5, -5, -5, -8, -5, -6, -5, -5, -5, -5, …
    $ dst   <chr> "A", "A", "A", "A", "A", "A", "A", "A", "U", "A", "A", "U", "A",…
    $ tzone <chr> "America/New_York", "America/Chicago", "America/Chicago", "Ameri…

### 5. Сколько компаний-перевозчиков (carrier) учитывают эти наборы данных (представлено в наборах данных)?

``` r
nycflights13::airlines %>% nrow()
```

    [1] 16

### 6. Сколько рейсов принял аэропорт John F Kennedy Intl в мае?

``` r
z <- filter(flights, month == 5 & origin == 'JFK')
count(z)
```

    # A tibble: 1 × 1
          n
      <int>
    1  9397

### 7. Какой самый северный аэропорт?

``` r
a <- max(nycflights13::airports$lat, na.rm = TRUE)
nycflights13::airports %>% select(name,lat) %>% filter(lat==a)
```

    # A tibble: 1 × 2
      name                      lat
      <chr>                   <dbl>
    1 Dillant Hopkins Airport  72.3

### 8. Какой аэропорт самый высокогорный (находится выше всех над уровнем моря)?

``` r
nycflights13::airports %>% arrange(desc(alt)) %>% select(name, alt) %>% top_n(1)
```

    Selecting by alt

    # A tibble: 1 × 2
      name        alt
      <chr>     <dbl>
    1 Telluride  9078

### 9. Какие бортовые номера у самых старых самолетов?

``` r
a <- filter(planes, year != 'NA')
b <- min(a$year)
c <- filter(planes, year == b)
select (c, tailnum)
```

    # A tibble: 1 × 1
      tailnum
      <chr>  
    1 N381AA 

### 10. Какая средняя температура воздуха была в сентябре в аэропорту John F Kennedy Intl (в градусах Цельсия).

``` r
a <- filter(weather, month == 9 , origin == 'JFK', temp != 'NA')
b <- summarize(a, delay = mean(temp, na.rm = TRUE ))
c <- 5/9*(b-32)
c
```

         delay
    1 19.38764

### 11. Самолеты какой авиакомпании совершили больше всего вылетов в июне?

``` r
a <- filter(flights, month == 6, carrier != 'NA', month != 'NA')
b <- group_by(a, carrier)
c <- count(b, carrier)
d <- filter(c, n == max(c$n))
f <- filter(airlines, carrier == d$carrier)
f
```

    # A tibble: 1 × 2
      carrier name                 
      <chr>   <chr>                
    1 UA      United Air Lines Inc.

### 12. Самолеты какой авиакомпании задерживались чаще других в 2013 году?

``` r
a <- filter(flights, year == 2013, dep_delay != 'NA', arr_delay != 'NA', arr_delay > 0, dep_delay > 0)
b <- group_by(a,carrier)
c <- count(b,carrier)
d <- filter(c, n == max(c$n))
f <- filter(airlines, carrier == d$carrier)
f
```

    # A tibble: 1 × 2
      carrier name                    
      <chr>   <chr>                   
    1 EV      ExpressJet Airlines Inc.
