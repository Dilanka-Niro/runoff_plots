```r
# Set working directory
setwd("~/Desktop/Dilanka_ayya")

# To work with xlsx
#install.packages("gdata") - Uncomment this if gdata is not working
require(gdata)
require(dplyr)

#test<-c(1:50)
#function
setlim<-function(x){if (x>30) {x<-30
  
}else x<-x}

#make a column for NA values
NA_bar<-function(x){if (x==0.12345) {x<-30

}else x<-0}
#filtered<-lapply(test, setlim)

# load precipitationdata for all years in dirrerent dataframes and Add a column for year, combine with runoff data

precipitation_Y1<-read.xls(xls = "Precipitation.xlsx",sheet = "Year 1")
precipitation_Y1$Year="Year 1"
precipitation_Y1[['Date']] <- as.POSIXct(precipitation_Y1[['Date']], format='%m/%d/%y')
runoff_y1<-read.xls(xls = "Runoff.xlsx",sheet = "Year 1")
runoff_y1[['Date']] <- as.POSIXct(runoff_y1[['Date']], format='%m/%d/%y')
all_data_Y1<-merge(precipitation_Y1,runoff_y1, by = "Date",sort = TRUE,all.x = TRUE )

precipitation_Y2<-read.xls(xls = "Precipitation.xlsx",sheet = "Year 2")
precipitation_Y2$Year="Year 2"
precipitation_Y2[['Date']] <- as.POSIXct(precipitation_Y2[['Date']], format='%m/%d/%y')
runoff_y2<-read.xls(xls = "Runoff.xlsx",sheet = "Year 2")
runoff_y2[['Date']] <- as.POSIXct(runoff_y2[['Date']], format='%m/%d/%y')
all_data_Y2<-merge(precipitation_Y2,runoff_y2, by = "Date",sort = TRUE,all.x = TRUE )

precipitation_Y3<-read.xls(xls = "Precipitation.xlsx",sheet = "Year 3")
precipitation_Y3$Year="Year 3"
precipitation_Y3[['Date']] <- as.POSIXct(precipitation_Y3[['Date']], format='%m/%d/%y')
runoff_y3<-read.xls(xls = "Runoff.xlsx",sheet = "Year 3")
runoff_y3[['Date']] <- as.POSIXct(runoff_y3[['Date']], format='%m/%d/%y')
all_data_Y3<-merge(precipitation_Y3,runoff_y3, by = "Date",sort = TRUE,all.x = TRUE )

precipitation_Y4<-read.xls(xls = "Precipitation.xlsx",sheet = "Year 4")
precipitation_Y4$Year="Year 4"
precipitation_Y4[['Date']] <- as.POSIXct(precipitation_Y4[['Date']], format='%m/%d/%y')
runoff_y4<-read.xls(xls = "Runoff.xlsx",sheet = "Year 4")
runoff_y4[['Date']] <- as.POSIXct(runoff_y4[['Date']], format='%m/%d/%y')
all_data_Y4<-merge(precipitation_Y4,runoff_y4, by = "Date",sort = TRUE,all.x = TRUE )




# Add them all in one dataframe
all_data<-rbind(all_data_Y1,all_data_Y2,all_data_Y3,all_data_Y4)

#make rain, snow and total variables
library("tidyverse")
final_data <- all_data %>%
  select(Date, Rain, Snow, Total, Year,Runoff.) %>%
  gather(key = "variable", value = "value", Rain, Snow, Total)

#Replace NA with 0
final_data[is.na(final_data)] = 0.12345

#read 'Date' in date format
final_data[['Date']] <- as.POSIXct(final_data[['Date']], format='%m/%d/%y')
final_data$runoff_filtered<-unlist(lapply(final_data$Runoff.,setlim))
#NA column for pecipitation
final_data$NA_prec<-unlist(lapply(final_data$value,NA_bar))
#NA column for runoff
final_data$NA_runoff<-unlist(lapply(final_data$runoff_filtered,NA_bar))
# load ggplot
library(ggplot2)

#create plot 
plot<-ggplot(final_data, aes(x = as.Date(Date), y = (60-value))) + 
  theme_bw()+
  geom_line(aes(color = variable,group=1)) +
  geom_line(data = final_data,aes(x = as.Date(Date), y = runoff_filtered))+
  geom_line(data = final_data,aes(x = as.Date(Date), y = (60-NA_prec)),color="white")+
  geom_line(data = final_data,aes(x = as.Date(Date), y = NA_runoff),color="white")+
  xlab("Date")+
  # set date breaks
  scale_x_date(date_breaks = "1 month", date_labels = "%b")+
  scale_color_manual(values = c("darkred", "steelblue","purple"))+
  
  #add second Y axis
  scale_y_continuous(limits = c(0,60),
    
    # Features of the first axis
    name = "Runoff",
    
    # Add a second axis and specify its features
    sec.axis = sec_axis(~.*-1.+60, name="Precipitation")
  )

#runoff_plot<-ggplot(final_data, aes(x = as.Date(Date), y =Runoff.)) + 
#  geom_line()
#final_runoff_plot<-runoff_plot+facet_wrap(vars(Year),scales = "free")

final_plot<-plot+facet_wrap(vars(Year),scales = "free")
  #ylim(0,20)
  #scale_x_datetime(labels = date_format("%b"))
  
ggsave(filename = "plot.pdf",plot = final_plot,height = 10, width = 20)
#ggsave(filename = "runoff_plot.pdf",plot = final_runoff_plot,height = 10, width = 20)
  
```
