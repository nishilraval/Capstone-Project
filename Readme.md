
# Using Neural Networks to Identify Botnets on a Network
![](https://upload.wikimedia.org/wikipedia/commons/3/36/Mirai-botnet-linked-to-massive-ddos-attacks-on-dyn-dns-gif.gif)
`2016 Mirai-Botnet DDoS attack`
    
## Directory Structure
```
..
├──Final_Report.md
├──Data Dictionary.md
├──Imports and methods notebook
├──Data Analysis and Preprocessing notebook
├──Packet Analysis notebook
├──Full project notebook (to be run in cloud)
├──Smaller sample notebook able to run on laptops
└──Sample Data Folder**
```  
**Sample data is provided because entire dataset is too large for github.   
[Link to entire CTU dataset](https://www.stratosphereips.org/datasets-ctu13/)  
  
  This repository contains the necessary notebooks and data to run the neural network classification model on a randomly generated sample of the total CTU dataset.

## Capstone Outline

This project is a identification model of botnets using bidirectional network flow files generated from a network.

A botnet is a "number of Internet-connected devices, each of which is running one or more bots. Botnets can be used to perform distributed denial-of-service attack (DDoS attack), steal data, send spam, and allows the attacker to access the device and its connection."  
[Wikipedia](https://en.wikipedia.org/wiki/Botnet)

![](https://upload.wikimedia.org/wikipedia/commons/thumb/6/67/Botnet_edit.svg/1920px-Botnet_edit.svg.png)  

    
Botnets have developed into a major problem with the "internet of things" concept becoming pervasive throughout most devices in the modern home. The security on these devices, ranging from web enabled security cameras to voice assistant microwaves, have been lackluster at best. The Mirai botnet (shown in the image at the top of the page) specifically targeted IoT consumer devices like as IP cameras and home routers.  
Because of this, it would be useful for consumers to be able to check their home networks for any suspicious activity. A botnet could slow down network performance, as well as IoT device performance, and possibly be financially damaging if data overages are incured because of the botnet internet activity.


Botnets can be detected by using behavior analysis on network traffic. Analyzing common patterns in network traffic can come up with generalized identification labels that become capable of revealing unknown botnet traffic on other networks.

## Files Outline


* *[Final_Report.md](Final_Report.md)*
> Current file. A technical summary of this project.

* *[Data_Dictionary.md](Data_Dictionary.md)*
> Data dictionary of bidirectional flow and pandas dataframe of this project.

* *Imports and methods notebook* 
> Jupyter notebook that includes all imports and methods.

* *Data Analysis and Preprocessing notebook* 
> Jupyter notebook that includes data analysis and feature engineering.

* *Packet Analysis notebook*
> Jupyter notebook that contains pcap file to pandas dataframe and analyzer.

* *Full project notebook (to be run in cloud)*
> Jupyter notebook to be run in google cloud compute instance to model on entire CTU-13 dataset.

* *Smaller sample notebook able to run on laptops*
> Jupyter notebook that includes all functions to model on random sample of smaller subset of CTU-13 dataset.

* *Binetflow Data Folder*
> Bidirectional network files for all scenarios.


## Data From CTU-13 Dataset
This dataset of botnet traffic was captured in the Czech Republic at CTU University. The dataset contains real botnet traffic mixed with background traffic. The botnets that were included were Neris, Rbot, Virut, Menti, Sogou, Murlo, and NSIS.ay. 
The bidirectional network flow files were created with the network auditing tool Argus. of all the traffic, including the labels. These binetflow files were fairly easy to change into CSV files and then into multiple pandas dataframe. The binetflow files were also labeled.  
  
### Data Dictionary
**Variable** | Meaning | Units
--- | --- | ---
StartTime | Date and time of day | Datetime
Dur | Duration of activity | Seconds (Float)
Proto | Transaction protocol | String
SrcAddr | Source IP Address | String
Sport | Source Port Integer | Integer
Dir | Direction of transaction | String
DstAddr | Destination IP Address | String
Dport | Destination Port Integer | Integer
State | State of device | String
sTos | Source TOS byte value. | Integer
dTos | Destination TOS byte value. | Integer
TotPkts | Total Packets | Integer
TotBytes |Total amount of bytes sent| Integer
SrcBytes | Source bytes | Integer
Label | Whether or not botnet | String  
  
![](https://upload.wikimedia.org/wikipedia/commons/8/86/Botnet_infection_of_Wifatch_from_loot_myself_twitter_post.png)  
  
``Worldwide Wifatch Botnet Infection``
   
## Data Analysis and Preprocessing

Through the initial exploratory data analysis I realized the dataset was much larger than I originally thought. The bidirectional flow files were seperated into 13 different scenarios. Each scenario uses a single botnet thats affected the network. The binetflow files for each scenario range from 13mb to 750mb in size. The supplied files were easily imported into a pandas dataframe with some simple additions of commas to the files. Each dataframe was put into list for easier data processing.
  
Then null values were dropped. It was debatable to drop them but I figured out that most of the nulls came from one column but dropping the Dtos column lost more information than dropping nulls instead.
  
The Label column was then changed from string to a boolean value of True or False. True indicates a botnet is sending that information on the network, while False means the device sending the information is not part of a botnet. The boolean value is able to be used at the y variable for modeling.

The source address and start time not used in this analysis. This is because the data was taken over a single day and the background data was considered synthetic. IP address spoofing is very easy and doable for most devices so I didn't want to rely on a variable that is easy to fool. 

The list of dataframes were appended together for use in the google cloud distributed compute instance.

When the dataframes were appended, completely identical rows were noticed so duplicates were removed. This is probably due to the synthetic background data.

The destination IP addresses were cut in half, using only the first six digits. A regex pattern was used for grabbing first 6 digits of IP address and assigning it to the same column. These first six digits indicate the node of the IP address.

Get_dummies function was used on the categorical values that had a managable amount of values. (Under 50)

Random sampling of minority and majority values while grouping on the Label column were done to mitigate the unbalanced classes. The value counts were:

**Normalized Value Count** | Label 
--- | --- | 
99% | False 
>1% | True

N_samples value was significantly lowered for small dataset notebook in order to be tested and run on laptops. 
The upsampled minority class were combined with the downsampled majority class.

The top 100 value counts of the three remaining categorical columns were used to dummy each of those categories. Then those dummy dataframe were joined to the sampled_df. The original dummied columns were dropped to save memory.




## Summary of Model

The train test split method was implemented on the random sampled data to attempt to minimize overfitting. K-fold validation was also used, but in combination with the random sampling and train, test, split it seemed like overly excessive. No significant difference was noticed from when kfold was included and when it wasn't, so it was removed to keep the notebooks more clean. 

When beginning the model a simple neural network was used with one hidden layer consisting of the shape of the training data and using a sigmoid activation for the classifying.

A for loop as well as a pipeline was used to try different variations of the neural network. This method was incredibly compute and time intensive. This was done on my desktop at home using mutliple GPUs as well as on the google cloud compute engine. While doing this process it because obvious through the results that the more complex the neural network the worse the overall accuracy of the model became.

Later, a recurrent neural network was used after doing research on different articles explaining machine learning classifications used on networks. The for loop and pipeline was used again to try different variations and settle on the final hyperparameters of the neural network.

The recurrent neural network was removed from the small sampled jupyter notebook due to memory errors that resulted when running on laptops.


## Future Evaluation
While the model works reliably there is a lot of room for improvement. While several different kind of botnets were used to gather the dataset many more exist in the real world. Finding and using different botnets could add to the model's accuracy. 

Improvements could be made to the loss function. Class weights were used to to minimize false negatives but I believe ensemble learning with sensitivity emphasis could lead to better results.


[Contact email](nishilraval@gmail.com)

# Citations:
* "An empirical comparison of botnet detection methods" Sebastian Garcia, Martin Grill, Jan Stiborek and Alejandro Zunino. Computers and Security Journal, Elsevier. 2014. Vol 45, pp 100-123. http://dx.doi.org/10.1016/j.cose.2014.05.011. 

* All images used fall under Creative Commons Attribution-Share Alike 3.0 Unported license or Creative Commons Attribution-Share Alike 4.0 International.
