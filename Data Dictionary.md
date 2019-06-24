## Data From CTU-13 Dataset
This dataset of botnet traffic was captured in the Czech Republic at CTU University. The dataset contains real botnet traffic mixed with background traffic. The botnets that were included were Neris, Rbot, Virut, Menti, Sogou, Murlo, and NSIS.ay.   
 
The bidirectional network flow files were created with the network auditing tool Argus. These binetflow files were changed into CSV files and then into multiple pandas dataframe. The binetflow files were also labeled.  
  
### Data Dictionary
**Variable** | Meaning | Units
--- | --- | ---
StartTime | Date and Time of Day | Datetime
Dur | Duration of Activity | Seconds (Float)
Proto | Transaction Protocol | String
SrcAddr | Source IP Address | String
Sport | Source Port Integer | Integer
Dir | Direction of Transaction | String
DstAddr | Destination IP Address | String
Dport | Destination Port Integer | Integer
State | State of Device | String
sTos | Source TOS Byte Value. | Integer
dTos | Destination TOS Byte Value. | Integer
TotPkts | Total Packets | Integer
TotBytes |Total Amount of Bytes Sent| Integer
SrcBytes | Source Bytes | Integer
Label | Whether or Not Botnet | String  


# Citations:
* "An empirical comparison of botnet detection methods" Sebastian Garcia, Martin Grill, Jan Stiborek and Alejandro Zunino. Computers and Security Journal, Elsevier. 2014. Vol 45, pp 100-123. http://dx.doi.org/10.1016/j.cose.2014.05.011. 