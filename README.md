Australia Fires
================
Jamie Hargreaves

``` r
library(tidyverse)
library(lubridate)
library(hrbrthemes)

# get data
temperature <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-01-07/temperature.csv')

# calculate average maximum summer temperature each year by city
mean_temp <- temperature %>% 
  
  # extract year and month from date
  mutate(year = year(date), month = month(date)) %>% 

  # filter on summer months - Dec, Jan and Feb and max temperature
  filter(month %in% c(12, 1, 2), temp_type == "max") %>% 
  
  # calculate average temperature each year by city
  group_by(city_name, year) %>% 
  summarise(mean = mean(temperature, na.rm = TRUE)) %>% 
  
  # get the maximum average for each city
  mutate(colour = ifelse(max(mean, na.rm = TRUE) == mean, TRUE, NA),
         
         # hottest year
         max_year = ifelse(max(mean, na.rm = TRUE) == mean, year, NA)) %>% 
  
  ungroup() %>% 
  
  # convert city_name from CAPS to Caps
  mutate(city_name = tools::toTitleCase(tolower(city_name)))

# visualisation
mean_temp %>% 
  
  ggplot(aes(x = year, y = mean)) +
  
  # line plot - maximum average temperature
  geom_line(colour = "darkgrey") +
  
  # add a point showing the year in which the maximum average occurred
  geom_point(aes(colour = colour), size = 2.5) +
  
  # label the point with the year
  geom_text(aes(y = mean + 2, label = max_year), colour = "orange") +
  
  # linear trend
  geom_smooth(method = "lm", se = FALSE, size = 0.5) +
  
  # turn off clipping
  coord_cartesian(clip = "off") +
  
  # set the point colours
  scale_colour_manual(values = c("orange", "NA"), na.translate = FALSE,
                      name = "", labels = c("Hottest Average Summer")) +
  
  # facet by city
  facet_wrap(. ~ city_name, ncol = 4) +
  
  # axis labels and title
  labs(x = "Year", y = "Average Maximum Temperature (°C)",
       title = "Are Australian Summers Getting Hotter?",
       subtitle = "In 6 of 7 major cities, the hottest average summer occurred within the last 5 years") +
  
  # alter theme 
  theme_ipsum(axis_text_size = 9) + 
  theme(legend.position = c(0.9, 0.2))
```

<img src="README_files/figure-gfm/unnamed-chunk-1-1.png" width="672" style="display: block; margin: auto;" />
