This is a final project that I completed with 4 others group members for my intro to data science class taken during the Fall 2022 semester. The prompt was to ask a question and 
answer it with an analytical approach using data. Our project aimed to produce a visual representation of market changes in plant oil production from the 1960s to 2019. 
We decided to focus on global production of Palm, Sunflower, Cotton, Olive, Soy, and Canola oil, in order to understand which commodity dominated or overtook the market through 
these times. I was involved in all elements of the project but took a leading role on the main coding elements including data manipulation and difficult graph production. 
We used R and tidyverse, dplyr, ggplot, to develop our visualizations.

Five different key figures were produced from our data analyzation. Three of these five are paired charts that show change across two snapshots in time.

To set up the analysis we first gathered the data and the support variables needed to make the country charts.

      oil <- read_csv('FAOSTAT_all_oil.csv')
      oil <- right_join(code, oil, by='Area')
      oil <- oil[c(1,3,10,12,14)]

  Data source - https://www.fao.org/faostat/en/#data/QCL
  
  Shape Data - https://www.naturalearthdata.com/downloads/110m-cultural-vectors/ and census.gov/

Figure 1 is a simple line chart tracking the production of six different seed oils from 1960 to 2000.
    
      total <- tibble(Year = 1961:2019, Palm=NA, Sunflower=NA, Cotton=NA, Olive=NA, Soy=NA, Canola=NA) 
      total[,'Palm'] <- (oil %>% filter(grepl('Palm oil', Item)) %>% na.omit() %>% group_by(Year) %>% summarise(sum(Value)))$`sum(Value)`
      total[,'Sunflower'] <- (oil %>% filter(grepl('Sunflower', Item)) %>% na.omit() %>% group_by(Year) %>% summarise(sum(Value)))$`sum(Value)`
      total[,'Cotton'] <- (oil %>% filter(grepl('Cotton', Item)) %>% na.omit() %>% group_by(Year) %>% summarise(sum(Value)))$`sum(Value)`
      total[,'Olive'] <- (oil %>% filter(grepl('Olive', Item)) %>% na.omit() %>% group_by(Year) %>% summarise(sum(Value)))$`sum(Value)`
      total[,'Soy'] <- (oil %>% filter(grepl('Soy', Item)) %>% na.omit() %>% group_by(Year) %>% summarise(sum(Value)))$`sum(Value)`
      total[,'Canola'] <- (oil %>% filter(grepl('Rapeseed', Item)) %>% na.omit %>% group_by(Year) %>% summarise(sum(Value)))$`sum(Value)` 

      ggplot(data = total) + 
        geom_line(aes(x = Year, y = Palm, color = 'Palm')) +
        geom_line(aes(x = Year, y = Sunflower, color = 'Sunflower')) +
        geom_line(aes(x = Year, y = Cotton, color = 'Cotton')) +
        geom_line(aes(x = Year, y = Olive, color = 'Olive')) +
        geom_line(aes(x = Year, y = Soy, color = 'Soy')) +
        geom_line(aes(x = Year, y = Canola, color = 'Canola')) +
        scale_color_manual(name = 'Oil Type', values = c('Palm' = 'red', 'Sunflower' = 'green', 'Cotton' = 'blue', 'Olive' = 'brown', 'Soy' = 'orange', 'Canola' = 'pink')) +
        labs(title = "Total World Oil Production", x = "Year", y = "tonnes") +
        theme(plot.title = element_text(hjust = .5))
  Figure 1
  <img width="885" alt="Screen Shot 2024-09-12 at 10 06 13 PM" src="https://github.com/user-attachments/assets/f2c010a8-5fba-4cb4-ad62-ebbc3f6ac2c2">


