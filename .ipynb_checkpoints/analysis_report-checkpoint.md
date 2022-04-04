---
layout: wide_default
---

# Midterm Analysis Report

## Types of Risk Measurements Chosen and their Properties 
#### <font color='green'>Types of Risk Exposures:</font>

I chose to analyze and measure three different risk categories that would be found within S&P 500 firms.

- **Supply Dependence Risk** (Risk associated with the dependence on the supply of certain   materials/products/labor that are needed for the firm to sufficiently run their business operations. Such disruption in supply or supply chain can significantly impact the firms economic standing.)<br/>
- **Competitor Based Risk** (Risk associated with firms that face intense competition.)<br/> 
- **Regulatory/Political Based Risk** (Risk associated with political or regulatory changes such as decisions made by the government regarding taxes, laws, and spending. I used three different variations to measure this risk.)

#### <font color='green'>How the Risk Exposures were measured:</font>

I used textual analysis to measure these risks. I first downloaded the annual reports (10K's) of all of the current S&P 500 companies (this is done in <font color='blue'>download_text_files.ipynb</font>). Then, I extracted the text from each 10K report and used regular expressions to count how many times certain words appeared next to eachother within the text. With a higher the count, that would imply that the given risk would be higher for that particular firm. (this is done in <font color='blue'>measure_risk.ipynb</font>)

For example, in measuring the **Supply Dependence Risk**, I used the following regular expression:

<font color='blue'>(['(supply|supplies|supplier|logistics|labor)','(risk|harm|adverse|negative|cost|suffer)'],15)</font>

This expression finds words such as "supply" and "labor" and if they are near any of the words such as "risk" and "adverse" (within 15 words), then that would add one to the total risk exposure of that firm. 

The following expressions were used in determining the other risk factors:

| Risk Factor| Word group 1 | Word group 2 | Max num words between groups |
| ----------- | ----------- | ----------- | ----------- |
| Competitor Based Risk| competitor,competition,competitive,position,customer | risk,harm,factor,adverse | 15 |
| Regulatory/Political Based Risk (1)| government,regulation,FDA,law,politic,compliance| risk,harm,cost,time,liability,adverse | 15 |
| Regulatory/Political Based Risk (2)| government,regulation,FDA,law,politic,compliance | risk,harm,cost,time,liability,adverse | 7 |
| Regulatory/Political Based Risk (3)| regulat,SEC,politic,international,tax | harm,negativ,adverse,decreas,susceptible | 15 |

#### <font color='green'>Reasoning For Chosen Risks:</font>
- **Supply Dependence Risk**<br/>
Supply chain was a large issue at the beginning of the covid outbreak. There are several firms that are more reliant on the supply of materials, labor, and other factors. I chose to measure this risk specifically because I wanted to see if there would be data to support my hypothesis that there would be lower returns for firms that depend on supply chain during the start of a pandemic-like outbreak.

- **Competitor Based Risk**<br/>
Almost all firms have competition, but some face more competition than others. I chose to measure this type of risk because I was interested in learning how competition within different industries and firms affected returns during economic downturn. 

- **Regulatory/Political Risk**<br/>
Regulatory issues is something most firms have to deal with, and come covid came an onslaught of new regulations such as mask mandates and policy controls. I was interested in capturing correlations that could suggest that firms with a higher exposure to this risk were negatively affected regarding their returns when covid started. 

#### <font color='green'>Statistical Properties:</font>
The following code will output a summary of statistical properties regarding the risk measurements.


```python
#Load output/sp500_accting_plus_textrisks.csv file
import seaborn as sns
import pandas as pd
from statsmodels.formula.api import ols as sm_ols

sample_csv = "output/sp500_accting_plus_textrisks.csv"
sample_firms_risks = pd.read_csv(sample_csv)

sample_firms_risks = sample_firms_risks.drop(['Unnamed: 0'], axis=1)

sample_firms_risks = sample_firms_risks.rename(columns={
                        'Regulatory and Political-based Risk Exposure': 'Regulatory Risk 1', 
                        'Regulatory and Political-based(2) Risk Exposure': 'Regulatory Risk 2',
                        'Regulatory and Political-based(3) Risk Exposure': 'Regulatory Risk 3',
                        'Competitor-based Risk Exposure': 'Competitor Risk',
                        'Supply Dependence-based Risk Exposure': 'Supply Risk'})

#Describe statistical data of the risks
sample_firms_risks[['Regulatory Risk 1',
       'Regulatory Risk 2',
       'Regulatory Risk 3',
       'Competitor Risk',
       'Supply Risk']].describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Regulatory Risk 1</th>
      <th>Regulatory Risk 2</th>
      <th>Regulatory Risk 3</th>
      <th>Competitor Risk</th>
      <th>Supply Risk</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>492.000000</td>
      <td>492.000000</td>
      <td>492.000000</td>
      <td>492.000000</td>
      <td>492.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>10.932927</td>
      <td>5.093496</td>
      <td>2.097561</td>
      <td>6.843496</td>
      <td>4.034553</td>
    </tr>
    <tr>
      <th>std</th>
      <td>9.564529</td>
      <td>5.839974</td>
      <td>2.103879</td>
      <td>5.361656</td>
      <td>4.016365</td>
    </tr>
    <tr>
      <th>min</th>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>5.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>3.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>8.000000</td>
      <td>3.000000</td>
      <td>2.000000</td>
      <td>6.000000</td>
      <td>3.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>13.000000</td>
      <td>6.000000</td>
      <td>3.000000</td>
      <td>9.000000</td>
      <td>6.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>71.000000</td>
      <td>39.000000</td>
      <td>15.000000</td>
      <td>35.000000</td>
      <td>27.000000</td>
    </tr>
  </tbody>
</table>
</div>



**As displayed in the output above of 492 datapoints, it is clear that I have a variation of risk measurements not only within risk categories, but between risk categories. For instance, all of my risk measurements have non-zero data within at least the 25% percentile, and a decently sized range from the 25%-75% percentile. This means that I have variation within my results, which is critical in measuring the potential correlation of these risk factors in respective to the firms returns. Further, the mean output for regulatory risk 1 is significantly different from regulatory risk 2 and 3, thus demonstrating the differences within my use of risk measurement.**


```python
reg1zero = sample_firms_risks[['Regulatory Risk 1']].value_counts()[0]
reg2zero = sample_firms_risks[['Regulatory Risk 2']].value_counts()[0]
reg3zero = sample_firms_risks[['Regulatory Risk 3']].value_counts()[0]
compzero = sample_firms_risks[['Competitor Risk']].value_counts()[0]
supplyzero = sample_firms_risks[['Supply Risk']].value_counts()[0]

data = {'Firms with no values': [reg1zero, reg2zero, reg3zero, compzero, supplyzero], 
        '% of firms with no values': [(reg1zero/492)*100, (reg2zero/492)*100, 
                                      (reg3zero/492)*100, (compzero/492)*100, (supplyzero/492)*100]}

df = pd.DataFrame(data, index =['Regulatory Risk 1',
                                'Regulatory Risk 2',
                                'Regulatory Risk 3',
                                'Competitor Risk',
                                'Supply Risk'])
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Firms with no values</th>
      <th>% of firms with no values</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Regulatory Risk 1</th>
      <td>5</td>
      <td>1.016260</td>
    </tr>
    <tr>
      <th>Regulatory Risk 2</th>
      <td>58</td>
      <td>11.788618</td>
    </tr>
    <tr>
      <th>Regulatory Risk 3</th>
      <td>117</td>
      <td>23.780488</td>
    </tr>
    <tr>
      <th>Competitor Risk</th>
      <td>14</td>
      <td>2.845528</td>
    </tr>
    <tr>
      <th>Supply Risk</th>
      <td>96</td>
      <td>19.512195</td>
    </tr>
  </tbody>
</table>
</div>



**Additionally, the above data table shows the number of firms that had a "0" value within the respective risk measurement. Considering that there are 492 firms, the amount of firms that had no values is quite low. Only 1% of firms had no "Regulatory Risk 1" and the maximum amout of "no values" was within the "Regulatory Risk 3" category with 23.8%.**

## Validation of the Risk Measurements
#### <font color='green'>Examples of Risk Matches:</font>

The following are several examples of risk matches that resulted from my defined expressions. In these examples, it is clear that the type of risk is found within the corporation. 

**Regulatory/Political Risk** </br>
- **Abbott (ABT):** 
"Abbott is subject to numerous **governmental regulations** and it can be **costly** to comply with these regulations and to develop compliant products and processes."

- **Bank of America (BAC):** 
"Economic or **geopolitical** stress in one or more countries could have a **negative** impact regionally or globally, resulting in, among other things, market volatility."

- **Boeing (BA):** 
"We conduct a significant portion of our business pursuant to U.S. **government** contracts, which are subject to unique **risks**."

**Competitor Risk** </br>
- **Assurant (AIZ):** 
"Significant **competitive** pressures, changes in customer preferences and disruption could **adversely** affect our results of operations."

- **Discover (DFS):** 
"These **competitive factors** affect our ability to attract and retain customers, increase usage of our products and maximize the revenue generated by our products."

- **Northrop Grumman(NOC)**
"If we are unable to attract and retain a qualified workforce, we may be unable to maintain our **competitive** position and our future success could be materially **adversely** affected."

**Supply Dependence Risk** </br>
-  **Medtronic (MDT)**
"A reduction or interruption in **supply**, and an inability to develop alternative sources for such supply, could **adversely** affect our ability to manufacture our products in a timely or cost-effective manner and could result in lost sales."

-  **Moderna (MRNA)**
"Failure to effectively maintain our cold-chain supply logistics, by us or third parties, has in the past and could in the future lead to additional manufacturing **costs** and delays in our ability to **supply** required quantities for clinical trials or otherwise."

- **Nike (NKE)**
"Continued volatility in the availability and prices for commodities and raw materials we use in our products and in our **supply** chain (such as cotton or petroleum derivatives) could have a material **adverse** effect on our costs, gross margins and profitability."


#### <font color='green'>Examples of Firms With High Risks:</font>
Using an arbitrary risk value, I have narrowed down the top ~10 firms who independently have the highest supply dependence, regulator/political, and competitor based risk.

**Firms With High Supply Dependence Risk** 


```python
high_supply_risk = sample_firms_risks.loc[sample_firms_risks['Supply Risk'] >= 16]
high_supply_risk = high_supply_risk[['Symbol', 'Security']]
high_supply_risk
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Symbol</th>
      <th>Security</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>48</th>
      <td>APTV</td>
      <td>Aptiv</td>
    </tr>
    <tr>
      <th>60</th>
      <td>BAX</td>
      <td>Baxter</td>
    </tr>
    <tr>
      <th>128</th>
      <td>CTRA</td>
      <td>Coterra</td>
    </tr>
    <tr>
      <th>176</th>
      <td>EL</td>
      <td>Estée Lauder Companies</td>
    </tr>
    <tr>
      <th>180</th>
      <td>ES</td>
      <td>Eversource</td>
    </tr>
    <tr>
      <th>181</th>
      <td>EXC</td>
      <td>Exelon</td>
    </tr>
    <tr>
      <th>321</th>
      <td>MOS</td>
      <td>Mosaic</td>
    </tr>
    <tr>
      <th>342</th>
      <td>NUE</td>
      <td>Nucor</td>
    </tr>
    <tr>
      <th>415</th>
      <td>SO</td>
      <td>Southern Company</td>
    </tr>
    <tr>
      <th>435</th>
      <td>TSLA</td>
      <td>Tesla</td>
    </tr>
  </tbody>
</table>
</div>



**Firms With High Regulatory/Political Risk** 


```python
high_reg_risk = sample_firms_risks.loc[sample_firms_risks['Regulatory Risk 1'] >= 40]
high_reg_risk = high_reg_risk[['Symbol', 'Security']]
high_reg_risk
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Symbol</th>
      <th>Security</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>59</th>
      <td>BAC</td>
      <td>Bank of America</td>
    </tr>
    <tr>
      <th>141</th>
      <td>DXCM</td>
      <td>DexCom</td>
    </tr>
    <tr>
      <th>237</th>
      <td>HII</td>
      <td>Huntington Ingalls Industries</td>
    </tr>
    <tr>
      <th>273</th>
      <td>LHX</td>
      <td>L3Harris</td>
    </tr>
    <tr>
      <th>278</th>
      <td>LDOS</td>
      <td>Leidos</td>
    </tr>
    <tr>
      <th>285</th>
      <td>LMT</td>
      <td>Lockheed Martin</td>
    </tr>
    <tr>
      <th>312</th>
      <td>MRNA</td>
      <td>Moderna</td>
    </tr>
    <tr>
      <th>378</th>
      <td>PTC</td>
      <td>PTC</td>
    </tr>
    <tr>
      <th>419</th>
      <td>STT</td>
      <td>State Street</td>
    </tr>
  </tbody>
</table>
</div>



**Firms With High Competitor Risk** 


```python
high_comp_risk = sample_firms_risks.loc[sample_firms_risks['Competitor Risk'] >= 22]
high_comp_risk = high_comp_risk[['Symbol', 'Security']]
high_comp_risk
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Symbol</th>
      <th>Security</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>50</th>
      <td>AIZ</td>
      <td>Assurant</td>
    </tr>
    <tr>
      <th>59</th>
      <td>BAC</td>
      <td>Bank of America</td>
    </tr>
    <tr>
      <th>118</th>
      <td>CMA</td>
      <td>Comerica</td>
    </tr>
    <tr>
      <th>144</th>
      <td>DFS</td>
      <td>Discover</td>
    </tr>
    <tr>
      <th>156</th>
      <td>DUK</td>
      <td>Duke Energy</td>
    </tr>
    <tr>
      <th>236</th>
      <td>HUM</td>
      <td>Humana</td>
    </tr>
    <tr>
      <th>237</th>
      <td>HII</td>
      <td>Huntington Ingalls Industries</td>
    </tr>
    <tr>
      <th>328</th>
      <td>NEM</td>
      <td>Newmont</td>
    </tr>
    <tr>
      <th>338</th>
      <td>NOC</td>
      <td>Northrop Grumman</td>
    </tr>
    <tr>
      <th>368</th>
      <td>PNC</td>
      <td>PNC Financial Services</td>
    </tr>
    <tr>
      <th>443</th>
      <td>TRV</td>
      <td>Travelers</td>
    </tr>
  </tbody>
</table>
</div>



#### <font color='green'>Discussion of the Risk Measurements:</font>
Based off of this data, the risk measurements are likely "valid" in terms of that they capture, generally, the predisposed risk of each firm in a quantifiable nature. First, in doing simple searches within a random firm's 10K, the areas where the firm addresses their risk regarding a specific topic are generally caught by the regular expression. It is easier to find areas where the regular expression correctly caught the risk presented than not. 

Furthermore, the outputs of the top ~10 firms of each risk, in majority, make sense.

For example, the top firms with **Supply Dependence Risk** mainly revolved around firms that had a large role in the energy (gas, electric) or auto industry, which makes complete sense. Energy and auto companies are dependent on supply or suppliers and would it would adversely affect them if they changed suddenly.

The top firms in the **Regulatory/Political Risk** were within a broad category of industries including pharmaceuticals, aerospace, engineering, and defense technology. Despite this broad range, it does make sense that these specific firms/industries would have a higher regulatory risk exposure.

The top firms in the **Competitor Risk** were companies such as Bank of America, Discover, Duke Energy, and PNC. Despite these firms being powerhouses in their respective industries, they do face intense competition which explains their high hit rate for the competitor risk category.

## Final Sample Description
#### <font color='green'>Retrieve the Final Sample of Observations:</font>

The code below will first calculate the compounded weekly returns of each firm from 03/09/2020 - 03/13/2020. It will then merge this data into the risk dataset (**sample_firms_risks**) to create one final dataframe containing all necessary observations (risk data, accounting data, return data). This dataframe is called **final_sample**. 


```python
#Read in the firm_returns dataset
firm_returns = pd.read_stata("2019-2020-stock_rets cleaned.dta")
firm_returns = firm_returns[['date', 'ticker', 'ret']]

#restrict dataset to show specified dates only
firm_returns = firm_returns[(firm_returns['date'] == 20200309) | 
                  (firm_returns['date'] == 20200310) | 
                  (firm_returns['date'] == 20200311) | 
                  (firm_returns['date'] == 20200312) | 
                  (firm_returns['date'] == 20200313)]

firm_returns['ret'] = pd.to_numeric(firm_returns['ret']) #make the 'ret' column a numeric type
firm_returns = firm_returns.assign(R = (1+firm_returns['ret'])) #add new column 'R' which is simply 1 + 'ret' col

#group the dataset by ticker
firm_returns = firm_returns.groupby(['ticker']) 

#compute weekly firm returns for each firm
wkly_firm_returns = firm_returns['R'].prod() - 1

#clean up DF
wkly_firm_returns = wkly_firm_returns.to_frame() #convert series into DF
wkly_firm_returns = wkly_firm_returns.rename(columns={'R': 'Weekly Return 03/09-03/13'})
wkly_firm_returns = wkly_firm_returns.reset_index()

#merge sample_firms_risks into the weekly return DF to get one final DF
final_sample = sample_firms_risks.merge(wkly_firm_returns,
                        left_on='Symbol',
                        right_on='ticker',
                        how='left')

final_sample = final_sample.drop(['ticker'],axis=1)
```

#### <font color='green'>Summary, Statistics, and EDA of the Final Sample:</font>

**Number of Firms in Dataset**


```python
print("There are",final_sample['Regulatory Risk 1'].count(),"number of firms with risk data, and",
     final_sample['Weekly Return 03/09-03/13'].count(),"firms with return data.")
```

    There are 492 number of firms with risk data, and 488 firms with return data.


**Summary, Statistics, EDA**


```python
print("The number of rows and variables within the final dataset is:",final_sample.shape,"\n")
print("The full list of variables (columns) in the dataset is:\n\n",final_sample.columns)
```

    The number of rows and variables within the final dataset is: (492, 47) 
    
    The full list of variables (columns) in the dataset is:
    
     Index(['Symbol', 'Security', 'Regulatory Risk 1', 'Regulatory Risk 2',
           'Regulatory Risk 3', 'Competitor Risk', 'Supply Risk', 'gvkey',
           'lpermno', 'datadate', 'fyear', 'sic', 'sic3', 'td', 'long_debt_dum',
           'me', 'l_a', 'l_sale', 'div_d', 'age', 'atr', 'smalltaxlosscarry',
           'largetaxlosscarry', 'l_emp', 'l_ppent', 'l_laborratio', 'Inv',
           'Ch_Cash', 'Div', 'Ch_Debt', 'Ch_Eqty', 'Ch_WC', 'CF', 'td_a', 'td_mv',
           'mb', 'prof_a', 'ppe_a', 'cash_a', 'xrd_a', 'dltt_a', 'invopps_FG09',
           'sales_g', 'dv_a', 'short_debt', '_merge', 'Weekly Return 03/09-03/13'],
          dtype='object')


**To understand what each of the variable names mean, please visit this link:</br> 
https://github.com/LeDataSciFi/ledatascifi-2022/blob/main/data/ccm_variable_descriptions.csv**

**The following table is the summary statistics of each variable within the dataset:**


```python
final_sample.describe().T
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>count</th>
      <th>mean</th>
      <th>std</th>
      <th>min</th>
      <th>25%</th>
      <th>50%</th>
      <th>75%</th>
      <th>max</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Regulatory Risk 1</th>
      <td>492.0</td>
      <td>10.932927</td>
      <td>9.564529</td>
      <td>0.000000</td>
      <td>5.000000</td>
      <td>8.000000</td>
      <td>13.000000</td>
      <td>7.100000e+01</td>
    </tr>
    <tr>
      <th>Regulatory Risk 2</th>
      <td>492.0</td>
      <td>5.093496</td>
      <td>5.839974</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>3.000000</td>
      <td>6.000000</td>
      <td>3.900000e+01</td>
    </tr>
    <tr>
      <th>Regulatory Risk 3</th>
      <td>492.0</td>
      <td>2.097561</td>
      <td>2.103879</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>2.000000</td>
      <td>3.000000</td>
      <td>1.500000e+01</td>
    </tr>
    <tr>
      <th>Competitor Risk</th>
      <td>492.0</td>
      <td>6.843496</td>
      <td>5.361656</td>
      <td>0.000000</td>
      <td>3.000000</td>
      <td>6.000000</td>
      <td>9.000000</td>
      <td>3.500000e+01</td>
    </tr>
    <tr>
      <th>Supply Risk</th>
      <td>492.0</td>
      <td>4.034553</td>
      <td>4.016365</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>3.000000</td>
      <td>6.000000</td>
      <td>2.700000e+01</td>
    </tr>
    <tr>
      <th>gvkey</th>
      <td>352.0</td>
      <td>45655.275568</td>
      <td>61312.551897</td>
      <td>1045.000000</td>
      <td>6357.250000</td>
      <td>13973.000000</td>
      <td>61612.250000</td>
      <td>3.160560e+05</td>
    </tr>
    <tr>
      <th>lpermno</th>
      <td>352.0</td>
      <td>53572.482955</td>
      <td>30231.047786</td>
      <td>10104.000000</td>
      <td>19474.750000</td>
      <td>58246.000000</td>
      <td>82644.250000</td>
      <td>9.343600e+04</td>
    </tr>
    <tr>
      <th>fyear</th>
      <td>352.0</td>
      <td>2018.886364</td>
      <td>0.317821</td>
      <td>2018.000000</td>
      <td>2019.000000</td>
      <td>2019.000000</td>
      <td>2019.000000</td>
      <td>2.019000e+03</td>
    </tr>
    <tr>
      <th>sic</th>
      <td>352.0</td>
      <td>4330.014205</td>
      <td>1949.495636</td>
      <td>100.000000</td>
      <td>2849.250000</td>
      <td>3786.000000</td>
      <td>5507.750000</td>
      <td>8.742000e+03</td>
    </tr>
    <tr>
      <th>sic3</th>
      <td>352.0</td>
      <td>432.784091</td>
      <td>194.979666</td>
      <td>10.000000</td>
      <td>284.750000</td>
      <td>378.500000</td>
      <td>550.750000</td>
      <td>8.740000e+02</td>
    </tr>
    <tr>
      <th>td</th>
      <td>352.0</td>
      <td>12119.868341</td>
      <td>21713.135840</td>
      <td>0.000000</td>
      <td>1817.975000</td>
      <td>5079.242500</td>
      <td>12152.750000</td>
      <td>1.884020e+05</td>
    </tr>
    <tr>
      <th>long_debt_dum</th>
      <td>352.0</td>
      <td>0.982955</td>
      <td>0.129625</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000e+00</td>
    </tr>
    <tr>
      <th>me</th>
      <td>352.0</td>
      <td>57432.237628</td>
      <td>116693.961167</td>
      <td>2963.886500</td>
      <td>13191.317000</td>
      <td>22455.288000</td>
      <td>51945.526250</td>
      <td>1.023856e+06</td>
    </tr>
    <tr>
      <th>l_a</th>
      <td>352.0</td>
      <td>9.708541</td>
      <td>1.229763</td>
      <td>6.569794</td>
      <td>8.800306</td>
      <td>9.686929</td>
      <td>10.557991</td>
      <td>1.322070e+01</td>
    </tr>
    <tr>
      <th>l_sale</th>
      <td>352.0</td>
      <td>9.317635</td>
      <td>1.256966</td>
      <td>4.097822</td>
      <td>8.472732</td>
      <td>9.231821</td>
      <td>10.018299</td>
      <td>1.314555e+01</td>
    </tr>
    <tr>
      <th>div_d</th>
      <td>352.0</td>
      <td>0.744318</td>
      <td>0.436865</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000e+00</td>
    </tr>
    <tr>
      <th>age</th>
      <td>352.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000e+00</td>
    </tr>
    <tr>
      <th>atr</th>
      <td>352.0</td>
      <td>0.234018</td>
      <td>0.235356</td>
      <td>0.000000</td>
      <td>0.125926</td>
      <td>0.199817</td>
      <td>0.241626</td>
      <td>1.000000e+00</td>
    </tr>
    <tr>
      <th>smalltaxlosscarry</th>
      <td>273.0</td>
      <td>0.721612</td>
      <td>0.449029</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000e+00</td>
    </tr>
    <tr>
      <th>largetaxlosscarry</th>
      <td>273.0</td>
      <td>0.197802</td>
      <td>0.399074</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1.000000e+00</td>
    </tr>
    <tr>
      <th>l_emp</th>
      <td>352.0</td>
      <td>3.327105</td>
      <td>1.160559</td>
      <td>0.455524</td>
      <td>2.440164</td>
      <td>3.263833</td>
      <td>4.197118</td>
      <td>6.025866e+00</td>
    </tr>
    <tr>
      <th>l_ppent</th>
      <td>352.0</td>
      <td>7.904878</td>
      <td>1.546549</td>
      <td>3.690204</td>
      <td>6.795529</td>
      <td>7.825421</td>
      <td>9.037236</td>
      <td>1.111335e+01</td>
    </tr>
    <tr>
      <th>l_laborratio</th>
      <td>352.0</td>
      <td>4.650632</td>
      <td>1.317926</td>
      <td>0.511044</td>
      <td>3.834218</td>
      <td>4.385719</td>
      <td>5.330502</td>
      <td>9.931146e+00</td>
    </tr>
    <tr>
      <th>Inv</th>
      <td>352.0</td>
      <td>0.054387</td>
      <td>0.084964</td>
      <td>-0.329408</td>
      <td>0.020732</td>
      <td>0.048013</td>
      <td>0.089258</td>
      <td>4.238831e-01</td>
    </tr>
    <tr>
      <th>Ch_Cash</th>
      <td>352.0</td>
      <td>0.008847</td>
      <td>0.065049</td>
      <td>-0.315808</td>
      <td>-0.007998</td>
      <td>0.003960</td>
      <td>0.023985</td>
      <td>3.837106e-01</td>
    </tr>
    <tr>
      <th>Div</th>
      <td>352.0</td>
      <td>0.025462</td>
      <td>0.027004</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.020492</td>
      <td>0.037559</td>
      <td>1.385936e-01</td>
    </tr>
    <tr>
      <th>Ch_Debt</th>
      <td>352.0</td>
      <td>0.014027</td>
      <td>0.072364</td>
      <td>-0.265326</td>
      <td>-0.019568</td>
      <td>0.000000</td>
      <td>0.031572</td>
      <td>4.217628e-01</td>
    </tr>
    <tr>
      <th>Ch_Eqty</th>
      <td>352.0</td>
      <td>-0.042757</td>
      <td>0.058526</td>
      <td>-0.282758</td>
      <td>-0.062731</td>
      <td>-0.023404</td>
      <td>-0.002746</td>
      <td>1.741915e-01</td>
    </tr>
    <tr>
      <th>Ch_WC</th>
      <td>352.0</td>
      <td>0.011417</td>
      <td>0.044580</td>
      <td>-0.252402</td>
      <td>-0.005327</td>
      <td>0.006631</td>
      <td>0.024374</td>
      <td>3.726431e-01</td>
    </tr>
    <tr>
      <th>CF</th>
      <td>352.0</td>
      <td>0.123344</td>
      <td>0.077283</td>
      <td>-0.288764</td>
      <td>0.074645</td>
      <td>0.113733</td>
      <td>0.162095</td>
      <td>3.332969e-01</td>
    </tr>
    <tr>
      <th>td_a</th>
      <td>352.0</td>
      <td>0.328435</td>
      <td>0.192516</td>
      <td>0.000000</td>
      <td>0.204722</td>
      <td>0.319227</td>
      <td>0.432302</td>
      <td>1.245754e+00</td>
    </tr>
    <tr>
      <th>td_mv</th>
      <td>352.0</td>
      <td>0.185137</td>
      <td>0.140769</td>
      <td>0.000000</td>
      <td>0.092256</td>
      <td>0.160139</td>
      <td>0.262688</td>
      <td>8.095309e-01</td>
    </tr>
    <tr>
      <th>mb</th>
      <td>352.0</td>
      <td>3.027132</td>
      <td>2.091248</td>
      <td>0.877849</td>
      <td>1.583681</td>
      <td>2.412643</td>
      <td>3.655906</td>
      <td>1.308288e+01</td>
    </tr>
    <tr>
      <th>prof_a</th>
      <td>352.0</td>
      <td>0.151264</td>
      <td>0.074569</td>
      <td>-0.323828</td>
      <td>0.101974</td>
      <td>0.138951</td>
      <td>0.186640</td>
      <td>3.903839e-01</td>
    </tr>
    <tr>
      <th>ppe_a</th>
      <td>352.0</td>
      <td>0.247675</td>
      <td>0.219468</td>
      <td>0.009521</td>
      <td>0.091756</td>
      <td>0.162726</td>
      <td>0.336247</td>
      <td>9.285623e-01</td>
    </tr>
    <tr>
      <th>cash_a</th>
      <td>352.0</td>
      <td>0.126782</td>
      <td>0.138790</td>
      <td>0.002073</td>
      <td>0.032281</td>
      <td>0.072970</td>
      <td>0.167961</td>
      <td>6.946123e-01</td>
    </tr>
    <tr>
      <th>xrd_a</th>
      <td>352.0</td>
      <td>0.031364</td>
      <td>0.050330</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.009533</td>
      <td>0.043306</td>
      <td>3.367946e-01</td>
    </tr>
    <tr>
      <th>dltt_a</th>
      <td>352.0</td>
      <td>0.295314</td>
      <td>0.181393</td>
      <td>0.000000</td>
      <td>0.176565</td>
      <td>0.283319</td>
      <td>0.386684</td>
      <td>1.071959e+00</td>
    </tr>
    <tr>
      <th>invopps_FG09</th>
      <td>331.0</td>
      <td>2.702297</td>
      <td>2.106053</td>
      <td>0.405435</td>
      <td>1.250688</td>
      <td>2.160294</td>
      <td>3.298536</td>
      <td>1.216423e+01</td>
    </tr>
    <tr>
      <th>sales_g</th>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>dv_a</th>
      <td>352.0</td>
      <td>0.025462</td>
      <td>0.027004</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.020492</td>
      <td>0.037559</td>
      <td>1.385936e-01</td>
    </tr>
    <tr>
      <th>short_debt</th>
      <td>346.0</td>
      <td>0.112720</td>
      <td>0.111597</td>
      <td>0.000000</td>
      <td>0.026783</td>
      <td>0.085531</td>
      <td>0.152638</td>
      <td>7.610294e-01</td>
    </tr>
    <tr>
      <th>Weekly Return 03/09-03/13</th>
      <td>488.0</td>
      <td>-0.122035</td>
      <td>0.090437</td>
      <td>-0.610145</td>
      <td>-0.159448</td>
      <td>-0.106716</td>
      <td>-0.065169</td>
      <td>1.177476e-01</td>
    </tr>
  </tbody>
</table>
</div>



**The following table shows the number of unique values for the specified variables:**


```python
final_sample[['Regulatory Risk 1','Regulatory Risk 2',
              'Regulatory Risk 3','Competitor Risk',
              'Supply Risk','Weekly Return 03/09-03/13']].nunique()
```




    Regulatory Risk 1             44
    Regulatory Risk 2             33
    Regulatory Risk 3             14
    Competitor Risk               31
    Supply Risk                   23
    Weekly Return 03/09-03/13    488
    dtype: int64



#### <font color='green'>Caveats About the Sample/Data:</font>

**1. Some of the 10K's were not properly downloaded.** </br>
Some of the 10K's that were downloaded are not actually full 10K reports and rather are a very short version of them, or a wrong document. For example the firms **"A"** and **"V"** both were not downloaded correctly. Both of these firms have "0" values for all of the risk data collected. This should not affect the overall data too much, since there are over 490 firms, however there could be other 10K reports downloaded incorrectly. </br> </br>
**2. Some of the accounting data is missing.** </br>
There are several columns within the accounting data that are clearly wrong. For example **sales_g** has all 'NaN' data, whereas the **age** category is all '0'. This won't affect my analysis as long as I do not use these types of data within my correlations.

## Exploring Correlation
#### <font color='green'>Return Data vs. Risk Values:</font>
**This section will use the weekly returns from 03/09 - 03/13 and risk data to but will measure potential correlations between the two variables.**

**Measurement: Supply Dependence Risk**


```python
ret_v_supply = sns.lmplot(data=final_sample,
               x='Supply Risk', y='Weekly Return 03/09-03/13').set(
               title="Firm Week Return v. Supply Dependence Risk ", 
               xlabel="Num Supply Dependence Risk Hits",
               ylabel="Firm Week Return 03/09-03/13")

ret_v_supply;
```


    
![png](output_29_0.png)
    


**Regression Analysis for Supply Dependence**


```python
sm_ols('Q("Weekly Return 03/09-03/13") ~ Q("Supply Risk")',data = final_sample).fit().summary()
```




<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>    <td>Q("Weekly Return 03/09-03/13")</td> <th>  R-squared:         </th> <td>   0.012</td>
</tr>
<tr>
  <th>Model:</th>                          <td>OLS</td>              <th>  Adj. R-squared:    </th> <td>   0.009</td>
</tr>
<tr>
  <th>Method:</th>                    <td>Least Squares</td>         <th>  F-statistic:       </th> <td>   5.657</td>
</tr>
<tr>
  <th>Date:</th>                    <td>Fri, 25 Mar 2022</td>        <th>  Prob (F-statistic):</th>  <td>0.0178</td> 
</tr>
<tr>
  <th>Time:</th>                        <td>12:06:52</td>            <th>  Log-Likelihood:    </th> <td>  483.60</td>
</tr>
<tr>
  <th>No. Observations:</th>             <td>   488</td>             <th>  AIC:               </th> <td>  -963.2</td>
</tr>
<tr>
  <th>Df Residuals:</th>                 <td>   486</td>             <th>  BIC:               </th> <td>  -954.8</td>
</tr>
<tr>
  <th>Df Model:</th>                     <td>     1</td>             <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>             <td>nonrobust</td>           <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
          <td></td>            <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>Intercept</th>        <td>   -0.1123</td> <td>    0.006</td> <td>  -19.439</td> <td> 0.000</td> <td>   -0.124</td> <td>   -0.101</td>
</tr>
<tr>
  <th>Q("Supply Risk")</th> <td>   -0.0024</td> <td>    0.001</td> <td>   -2.379</td> <td> 0.018</td> <td>   -0.004</td> <td>   -0.000</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td>172.409</td> <th>  Durbin-Watson:     </th> <td>   2.136</td> 
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.000</td>  <th>  Jarque-Bera (JB):  </th> <td> 662.686</td> 
</tr>
<tr>
  <th>Skew:</th>          <td>-1.578</td>  <th>  Prob(JB):          </th> <td>1.26e-144</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 7.758</td>  <th>  Cond. No.          </th> <td>    8.15</td> 
</tr>
</table><br/><br/>Notes:<br/>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.



**Measurement: Competitor Risk**


```python
ret_v_comp = sns.lmplot(data=final_sample,
               x='Competitor Risk', y='Weekly Return 03/09-03/13').set(
               title="Firm Week Return v. Competitor Risk ", 
               xlabel="Num Competitor Risk Hits",
               ylabel="Firm Week Return 03/09-03/13")
ret_v_comp;
```


    
![png](output_33_0.png)
    


**Regression Analysis for Competitor Risk**


```python
sm_ols('Q("Weekly Return 03/09-03/13") ~ Q("Competitor Risk")',data = final_sample).fit().summary()
```




<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>    <td>Q("Weekly Return 03/09-03/13")</td> <th>  R-squared:         </th> <td>   0.000</td>
</tr>
<tr>
  <th>Model:</th>                          <td>OLS</td>              <th>  Adj. R-squared:    </th> <td>  -0.002</td>
</tr>
<tr>
  <th>Method:</th>                    <td>Least Squares</td>         <th>  F-statistic:       </th> <td>  0.1401</td>
</tr>
<tr>
  <th>Date:</th>                    <td>Fri, 25 Mar 2022</td>        <th>  Prob (F-statistic):</th>  <td> 0.708</td> 
</tr>
<tr>
  <th>Time:</th>                        <td>12:06:53</td>            <th>  Log-Likelihood:    </th> <td>  480.84</td>
</tr>
<tr>
  <th>No. Observations:</th>             <td>   488</td>             <th>  AIC:               </th> <td>  -957.7</td>
</tr>
<tr>
  <th>Df Residuals:</th>                 <td>   486</td>             <th>  BIC:               </th> <td>  -949.3</td>
</tr>
<tr>
  <th>Df Model:</th>                     <td>     1</td>             <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>             <td>nonrobust</td>           <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
            <td></td>              <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>Intercept</th>            <td>   -0.1240</td> <td>    0.007</td> <td>  -18.682</td> <td> 0.000</td> <td>   -0.137</td> <td>   -0.111</td>
</tr>
<tr>
  <th>Q("Competitor Risk")</th> <td>    0.0003</td> <td>    0.001</td> <td>    0.374</td> <td> 0.708</td> <td>   -0.001</td> <td>    0.002</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td>166.003</td> <th>  Durbin-Watson:     </th> <td>   2.111</td> 
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.000</td>  <th>  Jarque-Bera (JB):  </th> <td> 602.293</td> 
</tr>
<tr>
  <th>Skew:</th>          <td>-1.536</td>  <th>  Prob(JB):          </th> <td>1.64e-131</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 7.492</td>  <th>  Cond. No.          </th> <td>    14.1</td> 
</tr>
</table><br/><br/>Notes:<br/>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.



**Measurement: Regulatory/Political Risk (all variations)**


```python
ret_v_reg1 = sns.lmplot(data=final_sample,
               x='Regulatory Risk 1', y='Weekly Return 03/09-03/13').set(
               title="Firm Week Return v. Regulatory Risk (1) ", 
               xlabel="Num Regulatory Risk (1) Hits",
               ylabel="Firm Week Return 03/09-03/13")
ret_v_reg1;

ret_v_reg2 = sns.lmplot(data=final_sample,
               x='Regulatory Risk 2', y='Weekly Return 03/09-03/13').set(
               title="Firm Week Return v. Regulatory Risk (2) ", 
               xlabel="Num Regulatory Risk (2) Hits",
               ylabel="Firm Week Return 03/09-03/13")
ret_v_reg2;

ret_v_reg3 = sns.lmplot(data=final_sample,
               x='Regulatory Risk 3', y='Weekly Return 03/09-03/13').set(
               title="Firm Week Return v. Regulatory Risk (3) ", 
               xlabel="Num Regulatory Risk (3) Hits",
               ylabel="Firm Week Return 03/09-03/13")
ret_v_reg3;
```


    
![png](output_37_0.png)
    



    
![png](output_37_1.png)
    



    
![png](output_37_2.png)
    


**Regression Analysis for Regulatory Risk (1)**


```python
sm_ols('Q("Weekly Return 03/09-03/13") ~ Q("Regulatory Risk 1")',data = final_sample).fit().summary()
```




<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>    <td>Q("Weekly Return 03/09-03/13")</td> <th>  R-squared:         </th> <td>   0.001</td>
</tr>
<tr>
  <th>Model:</th>                          <td>OLS</td>              <th>  Adj. R-squared:    </th> <td>  -0.001</td>
</tr>
<tr>
  <th>Method:</th>                    <td>Least Squares</td>         <th>  F-statistic:       </th> <td>  0.3067</td>
</tr>
<tr>
  <th>Date:</th>                    <td>Fri, 25 Mar 2022</td>        <th>  Prob (F-statistic):</th>  <td> 0.580</td> 
</tr>
<tr>
  <th>Time:</th>                        <td>12:06:56</td>            <th>  Log-Likelihood:    </th> <td>  480.93</td>
</tr>
<tr>
  <th>No. Observations:</th>             <td>   488</td>             <th>  AIC:               </th> <td>  -957.9</td>
</tr>
<tr>
  <th>Df Residuals:</th>                 <td>   486</td>             <th>  BIC:               </th> <td>  -949.5</td>
</tr>
<tr>
  <th>Df Model:</th>                     <td>     1</td>             <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>             <td>nonrobust</td>           <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
             <td></td>               <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>Intercept</th>              <td>   -0.1194</td> <td>    0.006</td> <td>  -19.226</td> <td> 0.000</td> <td>   -0.132</td> <td>   -0.107</td>
</tr>
<tr>
  <th>Q("Regulatory Risk 1")</th> <td>   -0.0002</td> <td>    0.000</td> <td>   -0.554</td> <td> 0.580</td> <td>   -0.001</td> <td>    0.001</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td>168.798</td> <th>  Durbin-Watson:     </th> <td>   2.118</td> 
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.000</td>  <th>  Jarque-Bera (JB):  </th> <td> 627.751</td> 
</tr>
<tr>
  <th>Skew:</th>          <td>-1.555</td>  <th>  Prob(JB):          </th> <td>4.85e-137</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 7.605</td>  <th>  Cond. No.          </th> <td>    22.1</td> 
</tr>
</table><br/><br/>Notes:<br/>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.



**Risk Data vs. Return Analysis** </br>
Interestingly, none of my risk factors I measured had a strong negative or positive correlation with the returns from 03/09 to 03/24. However, certain risks had slight negative correlations. The most significant one was with Supply Dependence Risk. As shown with the regression analysis there is an intercept of -0.1123 (mostly negative returns) and a slope of-0.0024. It is expected that this should be a negative slope since firms with a more dependence on supply and supply chains would be disrupted during the initial outbreak of covid. However, I did expect there to be a steeper slope.

In regards to all of the regulatory variations and competitor risk data, they showed almost no, or very slight correlations with the return data. This means that having this type of risk did not really affect the firms differently when the pandemic hit. In contrast, I expected specifically the regulatory risk to have a negative correlation compared to the weekly returns.

#### <font color='green'>Return Data vs. Other Firm Accounting Data:</font>
**This section will use the same type of analysis as presented before but will measure important accounting data to returns as opposed to risk data.**


**Measurement: PPE as a % of Assets**


```python
ret_v_ppe_a = sns.lmplot(data=final_sample,
               x='ppe_a', y='Weekly Return 03/09-03/13').set(
               title="Firm Week Return v. PPE as % of Assets", 
               xlabel="PPE as a % of Assets",
               ylabel="Firm Week Return 03/09-03/13")

ret_v_ppe_a;
```


    
![png](output_43_0.png)
    


**Measurement: Cash as a % of Assets**


```python
ret_v_cash_a = sns.lmplot(data=final_sample,
               x='cash_a', y='Weekly Return 03/09-03/13').set(
               title="Firm Week Return v. Cash as % of Assets", 
               xlabel="Cash as a % of Assets",
               ylabel="Firm Week Return 03/09-03/13")
ret_v_cash_a;
```


    
![png](output_45_0.png)
    


**Accounting Data vs. Return Analysis** </br>
I isolated two different accounting data fields in order to see correlations with weekly returns. The first one was PPE as a percentage of Assets. As expected, there is a negative correlation between the two. When a firm has more PPE on hand, that means the firm has more leverage and thus will be more susceptible to economic impacts (if market does poor, these firms do really poor and vise versa). In contrary, there is a positive correlation between returns and cash as a % of assets. Firms with higher cash on hand did better during the initial covid outbreak, which also can be expected.

#### <font color='green'>Return Data vs. Risk Values (Stimmy Day):</font>
**This section will use the same type of analysis as presented before but will measure the returns on 03/24 (stimulus day) as opposed to the previous 03/09 - 03/13.**


```python
#Read in the firm_returns dataset
firm_returns2 = pd.read_stata("2019-2020-stock_rets cleaned.dta")
firm_returns2 = firm_returns2[['date', 'ticker', 'ret']]

#restrict dataset to show specified dates only
firm_returns2 = firm_returns2[(firm_returns2['date'] == 20200324)]

firm_returns2['ret'] = pd.to_numeric(firm_returns2['ret']) #make the 'ret' column a numeric type
firm_returns2 = firm_returns2.assign(R = (1+firm_returns2['ret'])) #add new column 'R' which is simply 1 + 'ret' col

#group the dataset by ticker
firm_returns2 = firm_returns2.groupby(['ticker']) 

#compute weekly firm returns for each firm
wkly_firm_returns2 = firm_returns2['R'].prod() - 1

#clean up DF
wkly_firm_returns2 = wkly_firm_returns2.to_frame() #convert series into DF
wkly_firm_returns2 = wkly_firm_returns2.rename(columns={'R': 'Weekly Return 03/24'})
wkly_firm_returns2 = wkly_firm_returns2.reset_index()

#merge sample_firms_risks into the weekly return DF to get one final DF
final_sample_stimmy = sample_firms_risks.merge(wkly_firm_returns2,
                        left_on='Symbol',
                        right_on='ticker',
                        how='left')

final_sample_stimmy = final_sample_stimmy.drop(['ticker'],axis=1)
```

**Measurement: Supply Dependence Risk**


```python
ret_v_supply_stimmy = sns.lmplot(data=final_sample_stimmy,
               x='Supply Risk', y='Weekly Return 03/24').set(
               title="Firm Week Return v. Supply Dependence Risk ", 
               xlabel="Num Supply Dependence Risk Hits",
               ylabel="Firm Week Return 03/24")

ret_v_supply_stimmy;
```


    
![png](output_50_0.png)
    


**Measurement: Competitor Risk**


```python
ret_v_comp_stimmy = sns.lmplot(data=final_sample_stimmy,
               x='Competitor Risk', y='Weekly Return 03/24').set(
               title="Firm Week Return v. Competitor Risk ", 
               xlabel="Num Competitor Risk Hits",
               ylabel="Firm Week Return 03/24")
ret_v_comp_stimmy;
```


    
![png](output_52_0.png)
    


**Measurement: Regulatory/Political Risk (1)**


```python
ret_v_reg1_stimmy = sns.lmplot(data=final_sample_stimmy,
               x='Regulatory Risk 1', y='Weekly Return 03/24').set(
               title="Firm Week Return v. Regulatory Risk (1) ", 
               xlabel="Num Regulatory Risk (1) Hits",
               ylabel="Firm Week Return 03/24")
ret_v_reg1_stimmy;
```


    
![png](output_54_0.png)
    


**Measurement: PPE as a % of Assets**


```python
ret_v_ppe_a_stimmy = sns.lmplot(data=final_sample_stimmy,
               x='ppe_a', y='Weekly Return 03/24').set(
               title="Firm Week Return v. PPE as % of Assets", 
               xlabel="PPE as a % of Assets",
               ylabel="Firm Week Return 03/24")

ret_v_ppe_a_stimmy;
```


    
![png](output_56_0.png)
    


**Stimmy Day Return Data Analysis** </br>
With stimulus day announced on March 24, 2020, the returns over that period skyrocked (comparatively to previous returns in that month due to covid). Every single firm in my dataset had a positive return on that day. Thus, when analyzing the returns vs. several risk measurements, it would be predictable of such correlation to be the opposite of what my previous correlations displayed.

Interestingly, this proved to be somewhat correct. The PPE vs. returns switched to a postive correlation, and the Competitor Risk vs. returns switched to be a slight positive correlation as well. However the supply dependence risk showed no correlation at all, likewise with the regulatory risk.
