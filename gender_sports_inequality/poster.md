<<<<<<< HEAD

=======
poster
================

``` r
# Gender Inequality in College Sports
df <- read.csv("C:/Users/munmu/Documents/GitHub/data_viz/gender_sports_inequity/data/EADA_2020.csv")
```

``` r
df2 <- df
temp <- subset(df2, select = c(School, HDCOACH_SALARY_MEN, HDCOACH_SALARY_WOMEN)) %>% 
  group_by(School) %>% 
  summarize(Men = round(HDCOACH_SALARY_MEN/1000, digits=0), 
            Women = round(HDCOACH_SALARY_WOMEN/1000, digits=0)) %>% 
  arrange(Women) %>% 
  mutate(School = factor(School, 
                         levels = c('Dartmouth',
                                    'Yale',
                                    'Brown',
                                    'Columbia',
                                    'Cornell',
                                    'Harvard',
                                    'UPenn',
                                    'Princeton')))
melted_temp <- reshape2::melt(temp, id = 'School')

ggplot(melted_temp, aes(fill=variable, y=value, x=School)) + 
  geom_bar(position="dodge", stat="identity") + 
  scale_fill_viridis(discrete = T) + 
  coord_flip() + 
  my_theme() + 
  ylab("Annual Salary per Coach (in $ thousand)") + 
  labs(title = "For every school in the Ivy League, men's coaches have higher paid than women's coaches", 
       caption = "Source: U.S. Department of Education’s Equity in Athletics Data Analysis in 2020")
```

![](poster_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

``` r
df2 <- df
temp <- subset(df2, select = c(School, 
                               IL_OPEXPPERTEAM_MEN, IL_OPEXPPERTEAM_WOMEN, 
                               RECRUITEXP_MEN, RECRUITEXP_WOMEN, 
                               EXP_MEN, EXP_WOMEN)) %>% 
  group_by(School) %>% 
  summarize(EXP_MEN = round(EXP_MEN/(EXP_MEN+EXP_WOMEN), digits=2)*100, 
            RECRUITEXP_MEN = round(RECRUITEXP_MEN/(RECRUITEXP_MEN+RECRUITEXP_WOMEN), digits=2)*100, 
            OPEXPPERTEAM_MEN = round(IL_OPEXPPERTEAM_MEN/(IL_OPEXPPERTEAM_MEN+IL_OPEXPPERTEAM_WOMEN), digits=2)*100, 
            EXP_WOMEN = 100-EXP_MEN, 
            RECRUITEXP_WOMEN = 100-RECRUITEXP_MEN, 
            OPEXPPERTEAM_WOMEN = 100-OPEXPPERTEAM_MEN) %>% 
  arrange(EXP_WOMEN + RECRUITEXP_WOMEN + OPEXPPERTEAM_WOMEN) %>% 
  mutate(School = factor(School, 
                         levels = c('Yale',
                                    'Columbia',
                                    'UPenn',
                                    'Princeton',
                                    'Harvard',
                                    'Brown',
                                    'Cornell',
                                    'Dartmouth')))
melted_temp <- reshape2::melt(temp, id = 'School')
df_melted_temp <- melted_temp %>% 
  mutate(n = ifelse(grepl("EXP_WOMEN|RECRUITEXP_WOMEN|OPEXPPERTEAM_WOMEN", variable), value*-1, value)) %>% 
  mutate(gender = ifelse(grepl("EXP_WOMEN|RECRUITEXP_WOMEN|OPEXPPERTEAM_WOMEN", variable), 'Women', 'Men')) %>% 
  mutate(var = ifelse(grepl("OPEXPPERTEAM_WOMEN|OPEXPPERTEAM_MEN", variable), "Operating Expenses", 
               ifelse(grepl("RECRUITEXP_WOMEN|RECRUITEXP_MEN", variable), "Recruiting Expenses", "Total Expenses"))) %>% 
  mutate(var = factor(var, levels = c('Total Expenses','Operating Expenses','Recruiting Expenses')))

ggplot(df_melted_temp, aes(x = var, y = n, fill = gender)) + # Fill column
  geom_bar(stat = "identity", width = .8) + # Draw the bars
  geom_hline(yintercept = 0, linetype = 1) + 
  coord_flip() + # Flip axes
  labs(title = "For every school in the Ivy League, men’s expenditures exceed women’s expenditures", 
       subtitle = "Left: Women | Right: Men", 
       caption = "Source: U.S. Department of Education’s Equity in Athletics Data Analysis in 2020") + 
  scale_fill_viridis(discrete = T) + 
  my_theme() + 
  ylab("Expenditures of men’s and women’s teams (in percentage)") + 
  facet_wrap( ~ School, scales = "free_x")
```

![](poster_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

``` r
temp2 <- read.csv("C:/Users/munmu/Documents/GitHub/data_viz/gender_sports_inequity/data/PARTIC_Sport_Cat.csv")
temp2 <- subset(temp2, select = -c(PARTIC_MEN, PARTIC_WOMEN)) 
names(temp2)[names(temp2) == 'PCT_PARTIC_MEN'] <- "Men"
names(temp2)[names(temp2) == 'PCT_PARTIC_WOMEN'] <- "Women"
temp2 <- temp2 %>% 
  mutate(Sport = factor(Sport, 
                        levels = c('Volleyball',
                                   'Fencing',
                                   'Basketball',
                                   'Swimming and Diving',
                                   'Soccer',
                                   'Squash',
                                   'Tennis',
                                   'Rowing',
                                   'Ice Hockey',
                                   'Lacrosse')))
melted_temp <- melt(temp2, id = "Sport")

ggplot(melted_temp, aes(fill=variable, y=value*100, x=Sport)) + 
    geom_bar(position="stack", stat="identity") + 
    scale_fill_viridis(discrete = T) + 
    labs(title = "For all Ivy League schools, sports with highest women's participation", 
       caption = "Source: U.S. Department of Education’s Equity in Athletics Data Analysis in 2020") + 
    my_theme() + 
    theme(axis.text.x = element_text(angle = 90, hjust = 1, vjust = 0.6)) + 
    xlab("Men’s and women’s sports participation (in percentage)")
```

![](poster_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

``` r
temp2 <- read.csv("C:/Users/munmu/Documents/GitHub/data_viz/gender_sports_inequity/data/EXP_Sport_Cat.csv")
temp2 <- subset(temp2, select = -c(EXP_MEN, EXP_WOMEN))
names(temp2)[names(temp2) == 'PCT_EXP_MEN'] <- "Men"
names(temp2)[names(temp2) == 'PCT_EXP_WOMEN'] <- "Women"
temp2 <- temp2 %>% 
  mutate(Sport = factor(Sport, 
                        levels = c('Other Sports',
                                   'Ice Hockey',
                                   'Basketball',
                                   'Rowing',
                                   'Lacrosse',
                                   'Tennis',
                                   'Golf',
                                   'Water Polo',
                                   'Squash',
                                   'All Track Combined')))
melted_temp <- melt(temp2, id = "Sport")

ggplot(melted_temp, aes(fill=variable, y=value*100, x=Sport)) + 
  geom_bar(position="dodge2", stat="identity") + 
  scale_fill_viridis(discrete = T) + 
  my_theme() + 
  theme(axis.text.x = element_text(angle = 90, hjust = 1, vjust = 0.6)) + 
  xlab("Expenditures of men’s and women’s sports (in percentage)") + 
  labs(title = "For all Ivy League schools, sports with lowest women's spending", 
       caption = "Source: U.S. Department of Education’s Equity in Athletics Data Analysis in 2020")
```

![](poster_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->
>>>>>>> 960ad81 (first commit)
