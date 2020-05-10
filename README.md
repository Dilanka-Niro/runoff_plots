# Runoff_plots

```r
# Set working directory
setwd("~/Desktop/Dilanka_ayya")

# To work with xlsx
#install.packages("gdata") - Uncomment this if gdata is not working
require(gdata)
require(dplyr)

# load precipitationdata for all years in dirrerent dataframes and Add a column for year, combine with runoff data

precipitation_Y1<-read.xls(xls = "Precipitation.xlsx",sheet = "Year 1")
precipitation_Y1$Year="Year 1"
precipitation_Y1[['Date']] <- as.POSIXct(precipitation_Y1[['Date']], format='%m/%d/%y')
runoff_y1<-read.xls(xls = "Runoff.xlsx",sheet = "Year 1")
runoff_y1[['Date']] <- as.POSIXct(runoff_y1[['Date']], format='%m/%d/%y')
all_data_Y1<-merge(precipitation_Y1,runoff_y1, by = "Date",sort = TRUE )

precipitation_Y2<-read.xls(xls = "Precipitation.xlsx",sheet = "Year 2")
precipitation_Y2$Year="Year 2"
precipitation_Y2[['Date']] <- as.POSIXct(precipitation_Y2[['Date']], format='%m/%d/%y')
runoff_y2<-read.xls(xls = "Runoff.xlsx",sheet = "Year 2")
runoff_y2[['Date']] <- as.POSIXct(runoff_y2[['Date']], format='%m/%d/%y')
all_data_Y2<-merge(precipitation_Y2,runoff_y2, by = "Date",sort = TRUE )

precipitation_Y3<-read.xls(xls = "Precipitation.xlsx",sheet = "Year 3")
precipitation_Y3$Year="Year 3"
precipitation_Y3[['Date']] <- as.POSIXct(precipitation_Y3[['Date']], format='%m/%d/%y')
runoff_y3<-read.xls(xls = "Runoff.xlsx",sheet = "Year 3")
runoff_y3[['Date']] <- as.POSIXct(runoff_y3[['Date']], format='%m/%d/%y')
all_data_Y3<-merge(precipitation_Y3,runoff_y3, by = "Date",sort = TRUE )

precipitation_Y4<-read.xls(xls = "Precipitation.xlsx",sheet = "Year 4")
precipitation_Y4$Year="Year 4"
precipitation_Y4[['Date']] <- as.POSIXct(precipitation_Y4[['Date']], format='%m/%d/%y')
runoff_y4<-read.xls(xls = "Runoff.xlsx",sheet = "Year 4")
runoff_y4[['Date']] <- as.POSIXct(runoff_y4[['Date']], format='%m/%d/%y')
all_data_Y4<-merge(precipitation_Y4,runoff_y4, by = "Date",sort = TRUE )




# Add them all in one dataframe
all_data<-rbind(all_data_Y1,all_data_Y2,all_data_Y3,all_data_Y4)

#make rain, snow and total variables
library("tidyverse")
final_data <- all_data %>%
  select(Date, Rain, Snow, Total, Year,Runoff.) %>%
  gather(key = "variable", value = "value", Rain, Snow, Total)

#Replace NA with 0
final_data[is.na(final_data)] = 0

#read 'Date' in date format
final_data[['Date']] <- as.POSIXct(final_data[['Date']], format='%m/%d/%y')

# load ggplot
library(ggplot2)

#create plot 
plot<-ggplot(final_data, aes(x = as.Date(Date), y = value)) + 
  geom_line(aes(color = variable,group=1)) +
  # set date breaks
  scale_x_date(date_breaks = "1 month", date_labels = "%b")+
  scale_color_manual(values = c("darkred", "steelblue","purple"))

runoff_plot<-ggplot(final_data, aes(x = as.Date(Date), y =Runoff.)) + 
  geom_line()
final_runoff_plot<-runoff_plot+facet_wrap(vars(Year),scales = "free")

final_plot<-plot+facet_wrap(vars(Year),scales = "free")
  #ylim(0,20)
  #scale_x_datetime(labels = date_format("%b"))
  
ggsave(filename = "plot.pdf",plot = final_plot,height = 10, width = 20)
ggsave(filename = "runoff_plot.pdf",plot = final_runoff_plot,height = 10, width = 20)
  ```
