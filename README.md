Seed Oil Production Trends


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
    
      #A condensed version of the above process (change ggplot data = total2)
      total <- tibble(Year = 1961:2019, Palm=NA, Sunflower=NA, Cotton=NA, Olive=NA, Soy=NA, Canola=NA) 
      total[,'Palm'] <- (oil %>% filter(grepl('Palm oil', Item)) %>% na.omit() %>% group_by(Year) %>% summarise(sum(Value)))$`sum(Value)`
      total[,'Sunflower'] <- (oil %>% filter(grepl('Sunflower', Item)) %>% na.omit() %>% group_by(Year) %>% summarise(sum(Value)))$`sum(Value)`
      total[,'Cotton'] <- (oil %>% filter(grepl('Cotton', Item)) %>% na.omit() %>% group_by(Year) %>% summarise(sum(Value)))$`sum(Value)`
      total[,'Olive'] <- (oil %>% filter(grepl('Olive', Item)) %>% na.omit() %>% group_by(Year) %>% summarise(sum(Value)))$`sum(Value)`
      total[,'Soy'] <- (oil %>% filter(grepl('Soy', Item)) %>% na.omit() %>% group_by(Year) %>% summarise(sum(Value)))$`sum(Value)`
      total[,'Canola'] <- (oil %>% filter(grepl('Rapeseed', Item)) %>% na.omit %>% group_by(Year) %>% summarise(sum(Value)))$`sum(Value)` 

      #Make a line graph with 6 different lines, one for each type of oil given a specific corresponding color
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
  
  <img width="454" alt="Screen Shot 2024-09-12 at 10 15 50 PM" src="https://github.com/user-attachments/assets/961ae3d9-0e7d-4992-a067-0a13ec9f82f6">



Figure 2 is a stacked bar chart which was the most difficult of the charts to produce and one I was to able to figure out on my own.
In developing Figure 2, we took the big data set used in Figure 1 and then filtered it by year and type of plant oil to find the top 10 producers of each plant oil type for each year. 
We then counted how many times each country appeared in the top 10 in production for type of plant oil. To show this graphically, we took this count and made a stacked bar chart in 
descending order of the frequency a country appeared in the top 10 in production for all oil types. This represents who on a global scale dominates oil production, broken out by which types of plant oil they specialize in.

      #Row bind a new table together with the top 10 countries based on production values for each oil for each year
      big <- oil %>% na.omit() %>% filter(grepl("Palm oil", Item)) %>% group_by(Year) %>% arrange(desc(Value), .by_group = TRUE) %>% slice(1:10)
      big <- rbind(big, oil %>% na.omit() %>% filter(grepl("Cotton", Item)) %>% group_by(Year) %>% arrange(desc(Value), .by_group = TRUE) %>% slice(1:10))
      big <- rbind(big, oil %>% na.omit() %>% filter(grepl("Sunflower", Item)) %>% group_by(Year) %>% arrange(desc(Value), .by_group = TRUE) %>% slice(1:10))
      big <- rbind(big, oil %>% na.omit() %>% filter(grepl("Olive", Item)) %>% group_by(Year) %>% arrange(desc(Value), .by_group = TRUE) %>% slice(1:10))
      big <- rbind(big, oil %>% na.omit() %>% filter(grepl("Soy", Item)) %>% group_by(Year) %>% arrange(desc(Value), .by_group = TRUE) %>% slice(1:10))
      big <- rbind(big, oil %>% na.omit() %>% filter(grepl("Rapeseed", Item)) %>% group_by(Year) %>% arrange(desc(Value), .by_group = TRUE) %>% slice(1:10))

      #Make a new tibble with the amount of times each country was in the top 10 for each different type of oil
      cou <- big %>% group_by(Item) %>% count(Area)

      #Make a tibble of counts for each country to be used in the graph
      c <- cou %>% group_by(Area) %>% summarise(sum(n))
      c <- c %>% arrange(`sum(n)`)

      #Stacked bar graph command 
      cou %>% ggplot(aes(x = factor(Area, levels = c$Area), y = n, fill = Item, label = n)) + geom_col() + coord_flip() + geom_text(size = 2, position = position_stack(vjust =.5)) + 
      labs(title = "Number of Years Each Country Has Been\nTop 10 in Oil Production Based on Each Oil Type", y = "Number of Years in Top 10", x = "Country") +                         
      scale_fill_discrete(name = "Oil Type", labels = c("Cottonseed", "Olive", "Palm", "Canola", "Soy Bean", "Sunflower-seed")) + 
      theme(legend.position = "right", legend.key.size = unit(.2,'cm'), legend.key.width = unit(.25,'cm'), legend.title = element_text(size = 9), plot.title = element_text(hjust = .5))
Figure 2

<img width="510" alt="Screen Shot 2024-09-12 at 10 15 04 PM" src="https://github.com/user-attachments/assets/0697ef84-2ce1-438a-8148-eaa5d993ca4e">



To make Figure 3 we utilized the sf package in R in order to map data onto a graph of the countries. This paired figure, featured graphs from 1980 and 2019 of the palm oil production across the globe. We took our main dataset filtered by palm oil production and combined it with a dataset of three letter country codes so that it could be 
joined with shapefiles by the country codes to be put on a choropleth map. On a linear scale, the gradient was not particularly noticeable, so we altered it to a logarithmic scale.

      palmoil19 <- oil %>% filter(grepl("Palm oil", Item)) %>% filter(Year=='2019')
      code <- read_xlsx('Copy of Country Code.xlsx')
      colnames(code) <- c('Area','code','iso code')
      palmoil19 <- right_join(code, palmoil19, by='Area')
      palmoil19$`iso code`[48] <- 'CIV'
      palmoil19 <- palmoil19 %>% filter(Area!= 'China, mainland')

      map <- read_sf('World/Countries/ne_110m_admin_0_countries.shp')
      colnames(palmoil19) <- c('Area','code','SOV_A3','Value','Year')
      map19 <- left_join(map,palmoil19, by = 'SOV_A3')
      my_breaks <- c(500, 3000,10000,1200000,25000000)

      ggplot() +
        geom_sf(data=map19, aes(geometry=geometry, fill=(Value))) +
        ggtitle('2019 Palm Oil Production by Country') +
        scale_fill_gradient(name='Palm oil (tonnes)', trans='log', breaks=my_breaks)

This process was repeated for the year 1980 in order to obtain the second graph in this figure.

Figure 3

<img width="1005" alt="Screen Shot 2024-09-12 at 10 25 29 PM" src="https://github.com/user-attachments/assets/447eb692-e4b5-481f-b7bf-220885a26546">
<img width="1015" alt="Screen Shot 2024-09-12 at 10 25 51 PM" src="https://github.com/user-attachments/assets/1133bd3e-bceb-4ceb-8a51-e7fc359276f6">



Figure 4 followed a similar process to Figure 3 but instead of only using palmoil, each countries most produced oil type became what was displayed in the chloropleth. 
To make this change you must categorize each country in the data set by their biggest seed oil and then combine that with the country codes to produce the chart.
We once again did this for two different years to help illustrate the change over time between 1970 and 2019.

Figure 4


<img width="1000" alt="Screen Shot 2024-09-12 at 10 29 48 PM" src="https://github.com/user-attachments/assets/c4985d3d-0827-4d20-8443-309254ed35e8">
<img width="1001" alt="Screen Shot 2024-09-12 at 10 30 08 PM" src="https://github.com/user-attachments/assets/4c87b3df-ab1f-4839-84fd-de19e701b916">

In creating Figure 5a and 5b, we first made a new tibble from the large data set which had the total amount of plant oil produced for every year for each different type of oil. 
We then made another table which calculated the percentage of total plant oil production for each different type of oil for each year. Then using this new tibble we piped it into a 
ggplot defined to have multiple lines corresponding to all the different types of oil by color. The resulting image is Figure 5a. Figure 5b uses the same process but restricts the 
graph to only palm oil and represents each year as a singular dot.

Figure 5

<img width="1030" alt="Screen Shot 2024-09-12 at 10 33 53 PM" src="https://github.com/user-attachments/assets/19aa344e-648d-4efa-8a9d-97592c677338">


Ultimately, this project really showed how dominant the production of palm oil has become over the last 80 years. To go along with the technical side of this analysis we had many 
points about the environmental impact of farming these different types of seed oils with the primary purpose of showing how detrimental it is to let palm oil become the largest produced seed oil across the world.
