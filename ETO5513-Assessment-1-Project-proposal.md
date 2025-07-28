---
title: 'ETO5513 Assessment 1: Project Proposal'
author: "Megan O'Rorke 35602287"
date: "28/07/2025"
output:
  bookdown::html_document2:
    keep_md: true
    toc: true
    toc_float: true
    theme: journal
    highlight: kate
    # (Xie et al. 2023)
    # (Xie et al. 2025)
---





## Research Question

Self-harm is a serious public health concern globally, with prior research (Richardson et al. 2024) finding bullying victimisation, sleep disturbance, mental health disorders and identifying as part of the LGBTQI+ community to be key risk factors for self-harm and suicidality in young people.

This report aims to investigate which are the most significant risk factors associated with self-harm among 20–24 year-olds from 1990 to 2021, and how has their effect on deaths, years of life lost and years lived with disability changed over time?

## Data Set Introduction

The data set analysed in this report is a subset of the [Global Burden of Disease Study 2021](https://vizhub.healthdata.org/gbd-results/) exploring the influence of different risk factors on the number of deaths, years of life lost and years lived with disability due to self-harm in 20-24 year-olds between 1990 and 2021. Each observation contains the years of life lost, years lived with disability and death figures for a particular risk factor in a given year, across both sexes globally.

The data set includes three numerical variables: *Years of Life Lost (YLLs)*, measuring how many years of life were lost due to premature death attributed to self-harm, *Years Lived with Disability (YLDs)*, measuring the severity-weighted number of years individuals lived with health-loss attributed to self-harm, and *Deaths*, measuring the number of deaths attributed to self-harm. It also contains two categorical variables: *Risk Factor*, indicating the attribute or exposure causally related to an increase in self-harm (Global Burden of Disease Study 2021), and *Year*, which spans from 1990 to 2021. All variables are recorded globally for 20–24 year-olds of both sexes.

The variable names within this data set can be observed in Table \@ref(tab:load-the-data-and-clean) below:


``` r
gbd_data <- read.csv("./Data/GBD_data.csv")

# Remove unnecessary columns for metric, cause location, age, sex upper and lower
gbd_data2 <- gbd_data %>% 
  select(measure, rei, year, val)

# Use pivot wider to split the measure column values for deaths, YLL and YLD into their own columns so that each year/risk factor pair has only one observation across all 3 variables
gbd_data_wide <- gbd_data2 %>%
  # Use pivot_wider on the measure column to create new columns for each  
  pivot_wider(names_from = "measure", values_from = "val") %>%
  # Rename columns for clarity and streamlining
  rename(ylds = "YLDs (Years Lived with Disability)",
         ylls = "YLLs (Years of Life Lost)",
         deaths = "Deaths",
         risk_factor = "rei")

# Create a table using kable() and names() to display the variable names in the data set
knitr::kable(names(gbd_data_wide), col.names = "Variable Name", caption = "GBD Data Set Variables") 
```



Table: (\#tab:load-the-data-and-clean)GBD Data Set Variables

|Variable Name |
|:-------------|
|risk_factor   |
|year          |
|deaths        |
|ylds          |
|ylls          |

## Data Set Description

Within the subset of the Global Burden of Disease 2021 data explored in this report, there are **256 observations** across **5 variables**, as described in Table \@ref(tab:load-the-data-and-clean) above.


``` r
# Display the code image
knitr::include_graphics("./Image/Description Screenshot.png")
```

<img src="./Image/Description Screenshot.png" width="100%" style="display: block; margin: auto;" />

Of the 5 variables, *YLLs*, *YLDs* and *Deaths* are of numeric type, as most values are recorded with decimal points. The *Risk Factor* variable is of character type, representing the name of each risk category. The *Year* variable is stored as an integer but will be treated as a categorical variable when analysing trends and changes in YLLs, YLDs, and Deaths over time.


``` r
# Display first 2 data rows using str() and head () to set observation rows displayed to 2
str(head(gbd_data_wide, 2))
```

```
## tibble [2 × 5] (S3: tbl_df/tbl/data.frame)
##  $ risk_factor: chr [1:2] "High alcohol use" "High alcohol use"
##  $ year       : int [1:2] 1990 1991
##  $ deaths     : num [1:2] 5732 5853
##  $ ylds       : num [1:2] 3198 3233
##  $ ylls       : num [1:2] 387800 395917
```
## Data Summary

The data summary below explores the mean and sum statistics of deaths and YLLs for each risk factor of self-harm from 1990 to 2021, addressing the first portion of the Section \@ref(research-question) Research Question.


``` r
# Select risk_factor as the categorical variable and ylls and deaths as numeric
summary_data <- gbd_data_wide %>% 
  select(risk_factor, ylls, deaths) %>%
  # Group by risk_factor and create mean and sum statistics
  group_by(risk_factor) %>% 
  summarise(mean_ylls = mean(ylls, na.rm=TRUE), 
            sum_ylls = sum(ylls, na.rm=TRUE), 
            mean_deaths = mean(deaths, na.rm=TRUE), 
            sum_deaths = sum(deaths, na.rm=TRUE))

# Create a table of the summary_data and add caption
knitr::kable(head(summary_data, 10), caption = "Summary Statistics by Risk Factor", digits = 2)
```



Table: (\#tab:create-summary-data)Summary Statistics by Risk Factor

|risk_factor                      |  mean_ylls| sum_ylls| mean_deaths| sum_deaths|
|:--------------------------------|----------:|--------:|-----------:|----------:|
|All risk factors                 |  479554.01| 15345728|     7094.33|  227018.64|
|Behavioral risks                 |  556457.27| 17806633|     8232.21|  263430.57|
|Drug use                         |  162543.71|  5201399|     2404.82|   76954.12|
|Environmental/occupational risks |  -99460.72| -3182743|    -1471.74|  -47095.69|
|High alcohol use                 |  416568.95| 13330206|     6162.73|  197207.32|
|High temperature                 |  133511.72|  4272375|     1973.55|   63153.52|
|Low temperature                  | -237649.44| -7604782|    -3514.42| -112461.53|
|Non-optimal temperature          |  -99460.72| -3182743|    -1471.74|  -47095.69|

In Table \@ref(tab:create-summary-data) above, it can be observed that behavioural risks are associated with the most deaths and YLLs from self-harm, with a total of 263,431 deaths and 17,806,633 YLLs reported from 1990 to 2021. Conversely, environmental and temperature-related risks appear to have **negative** values, suggesting a non-attributable relationship with self-harm-related burdens.

## Visualisations

As explored in Table \@ref(tab:create-summary-data) above, behavioural risks such as tobacco use, child malnutrition, low physical activity, unsafe sex, dietary risks, domestic violence and bullying have the highest association with health burdens attributed to self-harm.

The line plot below depicts the trends in self-harm related deaths attributed to behavioural risks over time, from 1990 to 2021.


``` r
# Filter data to just behavioural risk observations
behavioural <- gbd_data_wide %>%
  # Filter to plot only the figures for behavioural risks
  filter(risk_factor == "Behavioral risks")

# Use ggplot to create line plot of the 3 health burdens from 1990-2021
  # Use ggplot to plot year on x axis and deaths, ylls and ylds as y axis values
ggplot(behavioural, aes(x=year, y=deaths)) +
  # Set line plot and colour
  geom_line(color = "Hot Pink") +
  # Use geom_point to add point mappings for each year
  geom_point(color = "Hot Pink") +
  # Scale x axis to ensure readability of year values
  scale_x_continuous(breaks = seq(1990, 2021, 2)) +
  # Scale y axis to remove scientific number formatting
  scale_y_continuous(labels = scales::comma) +
  # Set caption and axis labels
  labs(caption = "Self-Harm Related Deaths Attributed to Behavioural Risks, 1990-2021",
    x = "Year",
    y = "Number of Deaths") +
  # Set different theme
  theme_light() +
  # Make caption centered and larger (OpenAI 2024)
  theme(plot.caption = element_text(hjust = 0.5, size = 12))
```

<img src="Figures/line-plot-of-deaths-by-year-for-behavioural-risks-1.png" width="70%" style="display: block; margin: auto;" />

- *In the line plot above, it is observed that deaths from self-harm associated with behavioural risks had a positive association with year, gradually rising between 1990 and 1995. Deaths peaked in 1995 before declining and then peaking again in 2000.*
- *Since 2000, there has been a strong negative association between year and the number of self-harm deaths, likely due to the global implementation of various education and assistance programs that help mitigate the impact of behavioural risk factors.*


``` r
# Filter previous long version data set to just behavioural risk observations (so ylls,ylds & deaths are all under the original measures variable)
behavioural2 <- gbd_data2 %>%
  # Filter to plot only the figures for behavioural risks
  filter(rei == "Behavioral risks")
# Use ggplot to create basic plot
# Plot year on x axis, value on y axis, and ensure the fill of bars is set to each measure
bar_chart <- ggplot(behavioural2, aes(x=year, y=val, fill = measure)) +
  # Set bar position to fill for the proportion of each health burden
  geom_bar(stat = "identity", position = "fill") +
  # Scale x axis to ensure readability of year values
  scale_x_continuous(breaks = seq(1990, 2021, 5)) +
  labs(x = "Year", 
       y = "Proportion of Burden",
       fill = "Health Burden") +
  # Set theme
  theme_light() +
  # Move legend above plot so the width of plot is maintained
  theme(legend.position = "top")
# Use ggplotly from plotly package to make plot interactive
ggplotly(bar_chart) %>%
  # Adjust the layout of the legend in plotly so that the legend remains above the plot
  # (OpenAI 2024)
  layout(legend = list(
      # Set to horizontal legend
      orientation = "h",
      # Set the vertical (y) position of legend to above the plot
      y = 1.1))
```

<div class="figure" style="text-align: center">

```{=html}
<div class="plotly html-widget html-fill-item" id="htmlwidget-e06fea68e9396f0ff513" style="width:100%;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-e06fea68e9396f0ff513">{"x":{"data":[{"orientation":"v","width":[0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095],"base":[0.98557141883169774,0.98556866353511519,0.98556262756918001,0.98555449340098356,0.9855463936350457,0.98554258059508293,0.98554121133177031,0.98554059606248734,0.98553936881561877,0.98553615885795143,0.98553500444549735,0.98553665718362549,0.98553796298305829,0.98553811403487745,0.98553697060225953,0.98553489463802468,0.98553527608564517,0.98553414940996364,0.98553190064446228,0.98552968109937067,0.98552662431723004,0.98552597076354687,0.98552386396080016,0.98552253369794363,0.9855239604553282,0.98552528724644683,0.98552399402294344,0.98552278928932269,0.98552260983266093,0.98552294164490239,0.98552715321661399,0.98552804301264241],"x":[1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021],"y":[0.014428581168302257,0.014431336464884814,0.014437372430819995,0.014445506599016444,0.014453606364954297,0.014457419404917071,0.014458788668229694,0.01445940393751266,0.014460631184381234,0.014463841142048572,0.014464995554502647,0.014463342816374514,0.014462037016941709,0.014461885965122545,0.014463029397740468,0.014465105361975317,0.014464723914354827,0.014465850590036355,0.014468099355537722,0.014470318900629331,0.014473375682769962,0.014474029236453134,0.014476136039199838,0.014477466302056374,0.014476039544671804,0.014474712753553165,0.014476005977056561,0.014477210710677313,0.014477390167339066,0.014477058355097605,0.014472846783386006,0.014471956987357593],"text":["year: 1990<br />val: 0.014428581<br />measure: Deaths","year: 1991<br />val: 0.014431336<br />measure: Deaths","year: 1992<br />val: 0.014437372<br />measure: Deaths","year: 1993<br />val: 0.014445507<br />measure: Deaths","year: 1994<br />val: 0.014453606<br />measure: Deaths","year: 1995<br />val: 0.014457419<br />measure: Deaths","year: 1996<br />val: 0.014458789<br />measure: Deaths","year: 1997<br />val: 0.014459404<br />measure: Deaths","year: 1998<br />val: 0.014460631<br />measure: Deaths","year: 1999<br />val: 0.014463841<br />measure: Deaths","year: 2000<br />val: 0.014464996<br />measure: Deaths","year: 2001<br />val: 0.014463343<br />measure: Deaths","year: 2002<br />val: 0.014462037<br />measure: Deaths","year: 2003<br />val: 0.014461886<br />measure: Deaths","year: 2004<br />val: 0.014463029<br />measure: Deaths","year: 2005<br />val: 0.014465105<br />measure: Deaths","year: 2006<br />val: 0.014464724<br />measure: Deaths","year: 2007<br />val: 0.014465851<br />measure: Deaths","year: 2008<br />val: 0.014468099<br />measure: Deaths","year: 2009<br />val: 0.014470319<br />measure: Deaths","year: 2010<br />val: 0.014473376<br />measure: Deaths","year: 2011<br />val: 0.014474029<br />measure: Deaths","year: 2012<br />val: 0.014476136<br />measure: Deaths","year: 2013<br />val: 0.014477466<br />measure: Deaths","year: 2014<br />val: 0.014476040<br />measure: Deaths","year: 2015<br />val: 0.014474713<br />measure: Deaths","year: 2016<br />val: 0.014476006<br />measure: Deaths","year: 2017<br />val: 0.014477211<br />measure: Deaths","year: 2018<br />val: 0.014477390<br />measure: Deaths","year: 2019<br />val: 0.014477058<br />measure: Deaths","year: 2020<br />val: 0.014472847<br />measure: Deaths","year: 2021<br />val: 0.014471957<br />measure: Deaths"],"type":"bar","textposition":"none","marker":{"autocolorscale":false,"color":"rgba(248,118,109,1)","line":{"width":1.8897637795275593,"color":"transparent"}},"name":"Deaths","legendgroup":"Deaths","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"orientation":"v","width":[0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095],"base":[0.97612530369601003,0.97624528720183323,0.97651440754620955,0.97689163728108608,0.97724708266733984,0.97740368798336785,0.97746979036697246,0.97753034311142784,0.97768595729177854,0.97791095936651073,0.97801408117946287,0.97789503726736493,0.97774879802665737,0.97771530511736082,0.97778782050931512,0.97789345129602456,0.97779838335690528,0.97781500740809335,0.97789921517443257,0.97787334615106281,0.97793037683647799,0.9779318511480859,0.97797018280615877,0.97795304788361881,0.97792341133982885,0.97789598383222998,0.97804011736390772,0.97814972126369437,0.97819836114049719,0.97825404237938907,0.9781403622805358,0.978141436489258],"x":[1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021],"y":[0.009446115135687716,0.0093233763332819519,0.0090482200229704546,0.0086628561198974774,0.0082993109677058641,0.0081388926117150762,0.0080714209647978485,0.0080102529510595044,0.0078534115238402258,0.0076251994914406929,0.0075209232660344849,0.0076416199162605558,0.007789164956400918,0.0078228089175166327,0.0077491500929444124,0.0076414433420001204,0.007736892728739897,0.0077191420018702939,0.0076326854700297098,0.0076563349483078635,0.0075962474807520497,0.0075941196154609703,0.0075536811546413896,0.0075694858143248167,0.0076005491154993443,0.0076293034142168592,0.0074838766590357153,0.0073730680256283154,0.0073242486921637484,0.0072688992655133289,0.0073867909360781914,0.0073866065233844092],"text":["year: 1990<br />val: 0.009446115<br />measure: YLDs (Years Lived with Disability)","year: 1991<br />val: 0.009323376<br />measure: YLDs (Years Lived with Disability)","year: 1992<br />val: 0.009048220<br />measure: YLDs (Years Lived with Disability)","year: 1993<br />val: 0.008662856<br />measure: YLDs (Years Lived with Disability)","year: 1994<br />val: 0.008299311<br />measure: YLDs (Years Lived with Disability)","year: 1995<br />val: 0.008138893<br />measure: YLDs (Years Lived with Disability)","year: 1996<br />val: 0.008071421<br />measure: YLDs (Years Lived with Disability)","year: 1997<br />val: 0.008010253<br />measure: YLDs (Years Lived with Disability)","year: 1998<br />val: 0.007853412<br />measure: YLDs (Years Lived with Disability)","year: 1999<br />val: 0.007625199<br />measure: YLDs (Years Lived with Disability)","year: 2000<br />val: 0.007520923<br />measure: YLDs (Years Lived with Disability)","year: 2001<br />val: 0.007641620<br />measure: YLDs (Years Lived with Disability)","year: 2002<br />val: 0.007789165<br />measure: YLDs (Years Lived with Disability)","year: 2003<br />val: 0.007822809<br />measure: YLDs (Years Lived with Disability)","year: 2004<br />val: 0.007749150<br />measure: YLDs (Years Lived with Disability)","year: 2005<br />val: 0.007641443<br />measure: YLDs (Years Lived with Disability)","year: 2006<br />val: 0.007736893<br />measure: YLDs (Years Lived with Disability)","year: 2007<br />val: 0.007719142<br />measure: YLDs (Years Lived with Disability)","year: 2008<br />val: 0.007632685<br />measure: YLDs (Years Lived with Disability)","year: 2009<br />val: 0.007656335<br />measure: YLDs (Years Lived with Disability)","year: 2010<br />val: 0.007596247<br />measure: YLDs (Years Lived with Disability)","year: 2011<br />val: 0.007594120<br />measure: YLDs (Years Lived with Disability)","year: 2012<br />val: 0.007553681<br />measure: YLDs (Years Lived with Disability)","year: 2013<br />val: 0.007569486<br />measure: YLDs (Years Lived with Disability)","year: 2014<br />val: 0.007600549<br />measure: YLDs (Years Lived with Disability)","year: 2015<br />val: 0.007629303<br />measure: YLDs (Years Lived with Disability)","year: 2016<br />val: 0.007483877<br />measure: YLDs (Years Lived with Disability)","year: 2017<br />val: 0.007373068<br />measure: YLDs (Years Lived with Disability)","year: 2018<br />val: 0.007324249<br />measure: YLDs (Years Lived with Disability)","year: 2019<br />val: 0.007268899<br />measure: YLDs (Years Lived with Disability)","year: 2020<br />val: 0.007386791<br />measure: YLDs (Years Lived with Disability)","year: 2021<br />val: 0.007386607<br />measure: YLDs (Years Lived with Disability)"],"type":"bar","textposition":"none","marker":{"autocolorscale":false,"color":"rgba(0,186,56,1)","line":{"width":1.8897637795275593,"color":"transparent"}},"name":"YLDs (Years Lived with Disability)","legendgroup":"YLDs (Years Lived with Disability)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"orientation":"v","width":[0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095,0.90000000000009095],"base":[0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0],"x":[1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021],"y":[0.97612530369601003,0.97624528720183323,0.97651440754620955,0.97689163728108608,0.97724708266733984,0.97740368798336785,0.97746979036697246,0.97753034311142784,0.97768595729177854,0.97791095936651073,0.97801408117946287,0.97789503726736493,0.97774879802665737,0.97771530511736082,0.97778782050931512,0.97789345129602456,0.97779838335690528,0.97781500740809335,0.97789921517443257,0.97787334615106281,0.97793037683647799,0.9779318511480859,0.97797018280615877,0.97795304788361881,0.97792341133982885,0.97789598383222998,0.97804011736390772,0.97814972126369437,0.97819836114049719,0.97825404237938907,0.9781403622805358,0.978141436489258],"text":["year: 1990<br />val: 0.976125304<br />measure: YLLs (Years of Life Lost)","year: 1991<br />val: 0.976245287<br />measure: YLLs (Years of Life Lost)","year: 1992<br />val: 0.976514408<br />measure: YLLs (Years of Life Lost)","year: 1993<br />val: 0.976891637<br />measure: YLLs (Years of Life Lost)","year: 1994<br />val: 0.977247083<br />measure: YLLs (Years of Life Lost)","year: 1995<br />val: 0.977403688<br />measure: YLLs (Years of Life Lost)","year: 1996<br />val: 0.977469790<br />measure: YLLs (Years of Life Lost)","year: 1997<br />val: 0.977530343<br />measure: YLLs (Years of Life Lost)","year: 1998<br />val: 0.977685957<br />measure: YLLs (Years of Life Lost)","year: 1999<br />val: 0.977910959<br />measure: YLLs (Years of Life Lost)","year: 2000<br />val: 0.978014081<br />measure: YLLs (Years of Life Lost)","year: 2001<br />val: 0.977895037<br />measure: YLLs (Years of Life Lost)","year: 2002<br />val: 0.977748798<br />measure: YLLs (Years of Life Lost)","year: 2003<br />val: 0.977715305<br />measure: YLLs (Years of Life Lost)","year: 2004<br />val: 0.977787821<br />measure: YLLs (Years of Life Lost)","year: 2005<br />val: 0.977893451<br />measure: YLLs (Years of Life Lost)","year: 2006<br />val: 0.977798383<br />measure: YLLs (Years of Life Lost)","year: 2007<br />val: 0.977815007<br />measure: YLLs (Years of Life Lost)","year: 2008<br />val: 0.977899215<br />measure: YLLs (Years of Life Lost)","year: 2009<br />val: 0.977873346<br />measure: YLLs (Years of Life Lost)","year: 2010<br />val: 0.977930377<br />measure: YLLs (Years of Life Lost)","year: 2011<br />val: 0.977931851<br />measure: YLLs (Years of Life Lost)","year: 2012<br />val: 0.977970183<br />measure: YLLs (Years of Life Lost)","year: 2013<br />val: 0.977953048<br />measure: YLLs (Years of Life Lost)","year: 2014<br />val: 0.977923411<br />measure: YLLs (Years of Life Lost)","year: 2015<br />val: 0.977895984<br />measure: YLLs (Years of Life Lost)","year: 2016<br />val: 0.978040117<br />measure: YLLs (Years of Life Lost)","year: 2017<br />val: 0.978149721<br />measure: YLLs (Years of Life Lost)","year: 2018<br />val: 0.978198361<br />measure: YLLs (Years of Life Lost)","year: 2019<br />val: 0.978254042<br />measure: YLLs (Years of Life Lost)","year: 2020<br />val: 0.978140362<br />measure: YLLs (Years of Life Lost)","year: 2021<br />val: 0.978141436<br />measure: YLLs (Years of Life Lost)"],"type":"bar","textposition":"none","marker":{"autocolorscale":false,"color":"rgba(97,156,255,1)","line":{"width":1.8897637795275593,"color":"transparent"}},"name":"YLLs (Years of Life Lost)","legendgroup":"YLLs (Years of Life Lost)","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null}],"layout":{"margin":{"t":26.228310502283104,"r":7.3059360730593621,"b":40.182648401826491,"l":48.949771689497723},"plot_bgcolor":"rgba(255,255,255,1)","paper_bgcolor":"rgba(255,255,255,1)","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724},"xaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[1987.9549999999999,2023.0450000000001],"tickmode":"array","ticktext":["1990","1995","2000","2005","2010","2015","2020"],"tickvals":[1990,1995,2000,2005,2010,2015,2020],"categoryorder":"array","categoryarray":["1990","1995","2000","2005","2010","2015","2020"],"nticks":null,"ticks":"outside","tickcolor":"rgba(179,179,179,1)","ticklen":3.6529680365296811,"tickwidth":0.33208800332088001,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.68949771689498},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(222,222,222,1)","gridwidth":0.33208800332088001,"zeroline":false,"anchor":"y","title":{"text":"Year","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}},"hoverformat":".2f"},"yaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[-0.050000000000000003,1.05],"tickmode":"array","ticktext":["0.00","0.25","0.50","0.75","1.00"],"tickvals":[0,0.25,0.5,0.75,1],"categoryorder":"array","categoryarray":["0.00","0.25","0.50","0.75","1.00"],"nticks":null,"ticks":"outside","tickcolor":"rgba(179,179,179,1)","ticklen":3.6529680365296811,"tickwidth":0.33208800332088001,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.68949771689498},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(222,222,222,1)","gridwidth":0.33208800332088001,"zeroline":false,"anchor":"x","title":{"text":"Proportion of Burden","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}},"hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":"transparent","line":{"color":"rgba(179,179,179,1)","width":0.66417600664176002,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0,"y1":1}],"showlegend":true,"legend":{"bgcolor":"rgba(255,255,255,1)","bordercolor":"transparent","borderwidth":1.8897637795275593,"font":{"color":"rgba(0,0,0,1)","family":"","size":11.68949771689498},"title":{"text":"Health Burden","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}},"orientation":"h","y":1.1000000000000001},"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"source":"A","attrs":{"5da03bfa1cb2":{"x":{},"y":{},"fill":{},"type":"bar"}},"cur_data":"5da03bfa1cb2","visdat":{"5da03bfa1cb2":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.20000000000000001,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>
```

<p class="caption">(\#fig:stacked-bar-chart-burdens-by-year-for-behavioural-risks)Proportion of Self-Harm Related Burdens Attributed to Behavioural Risks, 1990-2021</p>
</div>
- *In Figure \@ref(fig:stacked-bar-chart-burdens-by-year-for-behavioural-risks) above, YLLs have annually contributed approximately 97.7% of the total health burdens reported from behavioural-risk-associated self-harm between 1990 and 2021.*
- *This consistency reflects that self-harm remains overwhelmingly fatal over time, causing a large number of premature deaths. YLLs dominate the data set, as they represent the number of years of life lost for each 20-24 year-old self-harm death - which is significant as this demographic are typically healthy with considerable years left to live.*

## Conclusions

The significance of risk factors associated with self-harm among 20-24 year-olds from 1990 to 2021 were analysed using the Global Burden of Disease Study 2021 data.

Behavioural risks returned the highest mean of 556,457.27 and sum of 17,806,633	years of life lost spanning from 1990-2021, and the highest mean of 8,232.21	and sum of 263,430.57 deaths from 1990-2021.

The effect of behavioural risks on deaths has changed over time, reporting a significant decline in total deaths from 2000 onwards.

These findings show that overall, behavioural risks were the most significant factor associated with self-harm in 20-24 year-olds from 1990 to 2021, however, their impact has reduced over time. As the world continues to become more adept in educating and implementing protective barriers for those susceptible to behavioural risks, the volume of health burdens associated with self-harm will continue to decrease. 

## References

Richardson, R., Connell, T., Foster, M., Blamires, J., Smita Keshoor, Moir, C. and Irene Suilan Zeng (2024). *Risk and Protective Factors of Self-harm and Suicidality in Adolescents: An Umbrella Review with Meta-Analysis.* Journal of youth and adolescence, 53(6). doi:<https://doi.org/10.1007/s10964-024-01969-w>.

Global Burden of Disease Collaborative Network (2021). *Global Burden of Disease Study 2021 (GBD 2021) Results.* Seattle, United States: Institute for Health Metrics and Evaluation (IHME). <https://vizhub.healthdata.org/gbd-results/>.

Xie, Y., Allaire, J.J., Grolemund, G. (2023). *R Markdown: The Definitive Guide.* Bookdown Org. <https://bookdown.org/yihui/rmarkdown/>

Xie, Y., Dervieux, C., Riederer, E. (2025). *R Markdown Cookbook.* Bookdown Org. <https://bookdown.org/yihui/rmarkdown-cookbook/>.

Rdocumentation Org (2019). *str: Compactly Display the Structure of an Arbitrary R Object*. Rdocumentation. <https://www.rdocumentation.org/packages/utils/versions/3.6.2/topics/str>.

OpenAI (2024) *ChatGPT* (GPT-4-turbo, May 2024 version)[Large language model]. https://chat.openai.com/
