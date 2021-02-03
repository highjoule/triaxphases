# File reading

Using tkinter can be quite covenient and adds some flexibility. As seen below, function namefile_read() extracts the name of the file, the path of the file and the extension. In the case of this example, the laboratory data is in an excel file. Since this is a common file, one can put some additional information like additional data regarding site or other technical specifications.

If an excel spreadsheet is the standard in a laboratory for triaxial tests data, this can be useful to work with. Although, this can easily read data from a .gds or a .txt file by making some simple changes to this code.

From my experience, getting the path and the name of the file can bring some advantages in the future like generation of new files of PDF reports.

## Columns in the triaxial file

Every triaxial apparatus will have its own standards while naming the columns, therefore is really important to follow the same way of columns nomenclature to be able to reproduce the code on future files.

In the list "plotheads" are called all the columns that are useful in the analisis of triaxial tests.


```python
try:
    import Tkinter as tk
except:
    import tkinter as tk
import os
import pandas as pd
from tkinter.filedialog import askopenfilename


tk.Tk().withdraw() # we don't want a full GUI, so keep the root window from appearing
filename = askopenfilename()

def namefile_read(filename):
    
    name_c,ext = os.path.splitext(filename) #splits the name of the file in name and extension
    name = os.path.basename(filename)
    path = os.path.dirname(filename) #extracts path of the file, all pdf files will be stored in the same path
    return [name,ext,path]


namefile=namefile_read(filename)[0]
ext=namefile_read(filename)[1]
path=namefile_read(filename)[2]

#heads of the columns needed for the plots, if these are not in the Rådata the program will end
plotheads = ['Radial Strain','Current Area','Load Cell','Radial Pressure','Back Volume','Back Pressure','Pore Pressure','Axial Stress','Axial Strain','Axial Displacement','Deviator Stress','Cambridge p','Eff, Cambridge p\'','Axial Force','Time since start of test','Max Shear Stress t','Eff, Axial Stress','Eff, Radial Stress','Mean Stress s','Volume Change']
```

## Reading columns and trim data

The next piece of code checks for the array called plotheads in UPPER CASE letters, in that case the column heads are again turn in the plotheads stated. Aditionally, first 7200 seconds are skipped, by doing this, record of the stage of saturation is avoided.


```python
from sklearn.linear_model import LinearRegression
import numpy as np

if 'Rawdata' in pd.ExcelFile(filename).sheet_names:
    #reads the sheet called Rådata and also trims the empty information if there is, 'any' means thta any empty value in the dataframe will be discarted                
    rawdf = pd.read_excel(filename,'Rawdata').dropna(axis=0,how='any')
    #next rows change the titles of the sheet so it be changed to readable headers
    rawtrim = rawdf.reset_index(drop=True)    
    title=list(rawtrim.iloc[0])
    titles_upp = list(rawtrim.iloc[0].apply(lambda x: x.upper()))#changes in CAPS all the headers so can compared to the ines needed
    df0 = rawtrim[1:].reset_index(drop=True)
    d=dict(zip(list(df0.columns),list(title)))
    df1 = df0.rename(columns=d)
    df = df1[plotheads]
    dftrim = df[int(np.where(df['Time since start of test'].values==7200)[0]):].reset_index(drop=True)



```

## Sample of excel spread sheet

![](https://github.com/highjoule/triaxread/blob/main/images/rawdata.PNG)


```python
dftrim
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Radial Strain</th>
      <th>Current Area</th>
      <th>Load Cell</th>
      <th>Radial Pressure</th>
      <th>Back Volume</th>
      <th>Back Pressure</th>
      <th>Pore Pressure</th>
      <th>Axial Stress</th>
      <th>Axial Strain</th>
      <th>Axial Displacement</th>
      <th>Deviator Stress</th>
      <th>Cambridge p</th>
      <th>Eff, Cambridge p'</th>
      <th>Axial Force</th>
      <th>Time since start of test</th>
      <th>Max Shear Stress t</th>
      <th>Eff, Axial Stress</th>
      <th>Eff, Radial Stress</th>
      <th>Mean Stress s</th>
      <th>Volume Change</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-0.1636</td>
      <td>1957.08</td>
      <td>0.002</td>
      <td>68.2</td>
      <td>-872</td>
      <td>45.1</td>
      <td>48.943</td>
      <td>69.2359</td>
      <td>0.1177</td>
      <td>0.1177</td>
      <td>1.0359</td>
      <td>68.5453</td>
      <td>19.6023</td>
      <td>0.1355</td>
      <td>7200</td>
      <td>0.518</td>
      <td>20.2929</td>
      <td>19.257</td>
      <td>19.775</td>
      <td>-872</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-0.1736</td>
      <td>1956.69</td>
      <td>0.002</td>
      <td>69.4</td>
      <td>-905</td>
      <td>46</td>
      <td>49.7</td>
      <td>70.4252</td>
      <td>0.1146</td>
      <td>0.1146</td>
      <td>1.0252</td>
      <td>69.7417</td>
      <td>20.0417</td>
      <td>0.1378</td>
      <td>7320</td>
      <td>0.5126</td>
      <td>20.7252</td>
      <td>19.7</td>
      <td>20.2126</td>
      <td>-905</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-0.1838</td>
      <td>1956.29</td>
      <td>0.0019</td>
      <td>70.6</td>
      <td>-937.5</td>
      <td>46.5</td>
      <td>50.306</td>
      <td>71.5642</td>
      <td>0.1108</td>
      <td>0.1108</td>
      <td>0.9642</td>
      <td>70.9214</td>
      <td>20.6154</td>
      <td>0.14</td>
      <td>7440</td>
      <td>0.4821</td>
      <td>21.2582</td>
      <td>20.294</td>
      <td>20.7761</td>
      <td>-937.5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-0.1914</td>
      <td>1955.98</td>
      <td>0.0019</td>
      <td>71.4</td>
      <td>-968</td>
      <td>47.2</td>
      <td>50.913</td>
      <td>72.3933</td>
      <td>0.1108</td>
      <td>0.1108</td>
      <td>0.9933</td>
      <td>71.7311</td>
      <td>20.8181</td>
      <td>0.1416</td>
      <td>7560</td>
      <td>0.4967</td>
      <td>21.4803</td>
      <td>20.487</td>
      <td>20.9837</td>
      <td>-968</td>
    </tr>
    <tr>
      <th>4</th>
      <td>-0.203</td>
      <td>1955.53</td>
      <td>0.0018</td>
      <td>72.6</td>
      <td>-999.5</td>
      <td>47.9</td>
      <td>51.67</td>
      <td>73.535</td>
      <td>0.104</td>
      <td>0.104</td>
      <td>0.935</td>
      <td>72.9117</td>
      <td>21.2417</td>
      <td>0.1438</td>
      <td>7680</td>
      <td>0.4675</td>
      <td>21.865</td>
      <td>20.93</td>
      <td>21.3975</td>
      <td>-999.5</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1203</th>
      <td>7.21</td>
      <td>2256.84</td>
      <td>0.0947</td>
      <td>137.2</td>
      <td>-4323.5</td>
      <td>88.4</td>
      <td>123.493</td>
      <td>179.144</td>
      <td>14.9138</td>
      <td>14.9138</td>
      <td>41.9443</td>
      <td>151.181</td>
      <td>27.6884</td>
      <td>0.4043</td>
      <td>151560</td>
      <td>20.9722</td>
      <td>55.6513</td>
      <td>13.707</td>
      <td>34.6792</td>
      <td>-4323.5</td>
    </tr>
    <tr>
      <th>1204</th>
      <td>7.2226</td>
      <td>2257.36</td>
      <td>0.0953</td>
      <td>137.2</td>
      <td>-4323.5</td>
      <td>88.6</td>
      <td>123.493</td>
      <td>179.413</td>
      <td>14.9336</td>
      <td>14.9336</td>
      <td>42.2128</td>
      <td>151.271</td>
      <td>27.7779</td>
      <td>0.405</td>
      <td>151680</td>
      <td>21.1064</td>
      <td>55.9198</td>
      <td>13.707</td>
      <td>34.8134</td>
      <td>-4323.5</td>
    </tr>
    <tr>
      <th>1205</th>
      <td>7.2354</td>
      <td>2257.91</td>
      <td>0.0954</td>
      <td>137.2</td>
      <td>-4323.5</td>
      <td>88.6</td>
      <td>123.493</td>
      <td>179.458</td>
      <td>14.9541</td>
      <td>14.9541</td>
      <td>42.2581</td>
      <td>151.286</td>
      <td>27.793</td>
      <td>0.4052</td>
      <td>151800</td>
      <td>21.1291</td>
      <td>55.9651</td>
      <td>13.707</td>
      <td>34.8361</td>
      <td>-4323.5</td>
    </tr>
    <tr>
      <th>1206</th>
      <td>7.2484</td>
      <td>2258.45</td>
      <td>0.0948</td>
      <td>137.2</td>
      <td>-4323.5</td>
      <td>88.6</td>
      <td>123.341</td>
      <td>179.194</td>
      <td>14.9746</td>
      <td>14.9746</td>
      <td>41.9935</td>
      <td>151.198</td>
      <td>27.8568</td>
      <td>0.4047</td>
      <td>151920</td>
      <td>20.9968</td>
      <td>55.8525</td>
      <td>13.859</td>
      <td>34.8558</td>
      <td>-4323.5</td>
    </tr>
    <tr>
      <th>1207</th>
      <td>7.2604</td>
      <td>2258.96</td>
      <td>0.0943</td>
      <td>137.2</td>
      <td>-4323.5</td>
      <td>88.8</td>
      <td>123.493</td>
      <td>178.932</td>
      <td>14.9936</td>
      <td>14.9936</td>
      <td>41.7321</td>
      <td>151.111</td>
      <td>27.6177</td>
      <td>0.4042</td>
      <td>152040</td>
      <td>20.8661</td>
      <td>55.4391</td>
      <td>13.707</td>
      <td>34.5731</td>
      <td>-4323.5</td>
    </tr>
  </tbody>
</table>
<p>1208 rows × 20 columns</p>
</div>



# Slope analysis

In theory the consolidation phase will not experience axial displacement, although there a slope will be close to zero will not be always zero. During the shear phase the sample experiences axial deformation, the sudden change of slope between consolidation and shearing is the objective in the next code entries. In the graph below, is shown this point.


```python
import matplotlib.pyplot as plt
fig = plt.figure(figsize=(5,3.5))
plt.plot(dftrim['Time since start of test']/3600,dftrim['Axial Displacement'],label = 'Axial displacement',linewidth=0.8)
plt.legend(bbox_to_anchor=(1.02, 1), loc=2, borderaxespad=0)
plt.ylabel('displacement (mm)')
plt.xlabel('Time (hrs)')
plt.grid(b=True, which='major', color='#A79E9E', linestyle='-', linewidth=0.4)
plt.minorticks_on()
plt.grid(b=True, which='minor', color='#999999', linestyle='-', alpha=0.2)
plt.show()
```


![](https://github.com/highjoule/triaxread/blob/main/images/axial%20displacement.png)



# Gradient comparison

The function below, takes step by step the time of the test and evaluates the slope of 100 steps ahead. When the value of the slope is above 0.00015 a second if evaluates if the slope of the 100 values behind is close to zero, in this case 1e-07. When both are true, the point of change between consolidation and shearing is found.


![](https://github.com/highjoule/triaxread/blob/main/images/axial%20displacement_1.png)

As seen below, the code shows a value of 20.63 hrs. Which can be verified by observing the graph of time vs displacement.


```python
from scipy.stats import linregress
def grad_cons1(dftrim):
    i = 101
    x,y = np.array(dftrim['Time since start of test'].astype(float)),np.array(dftrim['Axial Displacement'].astype(float))
    for j in range(len(dftrim['Time since start of test'])):
        if linregress(x[i:i+100],y[i:i+100])[0] > 0.00015:
            a = (linregress(x[i:i+100],y[i:i+100])[0],linregress(x[i-100:i],y[i-100:i])[0])
            b = np.std(a)
            if b < 1e-07:
                break
            else:
                pass
                i+=1
        else:
            pass
            i+=1
    return i-100
            
gr_indcon=grad_cons1(dftrim)
numconvol = dftrim.iloc[gr_indcon]['Time since start of test']
numconvol/3600
```




    20.634



# Phases separation

With this point identified, the next code splits two data sets, one of consolidation and a second of shearing.


```python
indconvol = grad_cons(df,dftrim)
numconvol = dftrim.iloc[indconvol]['Volume Change']
#dataframe for consolidation pahse
dfcons = dftrim.iloc[0:indconvol,:]
#dataframe for shearing phase
dfshear = dftrim.iloc[indconvol:,:]
```


```python
dfcons
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Radial Strain</th>
      <th>Current Area</th>
      <th>Load Cell</th>
      <th>Radial Pressure</th>
      <th>Back Volume</th>
      <th>Back Pressure</th>
      <th>Pore Pressure</th>
      <th>Axial Stress</th>
      <th>Axial Strain</th>
      <th>Axial Displacement</th>
      <th>Deviator Stress</th>
      <th>Cambridge p</th>
      <th>Eff, Cambridge p'</th>
      <th>Axial Force</th>
      <th>Time since start of test</th>
      <th>Max Shear Stress t</th>
      <th>Eff, Axial Stress</th>
      <th>Eff, Radial Stress</th>
      <th>Mean Stress s</th>
      <th>Volume Change</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-0.1636</td>
      <td>1957.08</td>
      <td>0.002</td>
      <td>68.2</td>
      <td>-872</td>
      <td>45.1</td>
      <td>48.943</td>
      <td>69.2359</td>
      <td>0.1177</td>
      <td>0.1177</td>
      <td>1.0359</td>
      <td>68.5453</td>
      <td>19.6023</td>
      <td>0.1355</td>
      <td>7200</td>
      <td>0.518</td>
      <td>20.2929</td>
      <td>19.257</td>
      <td>19.775</td>
      <td>-872</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-0.1736</td>
      <td>1956.69</td>
      <td>0.002</td>
      <td>69.4</td>
      <td>-905</td>
      <td>46</td>
      <td>49.7</td>
      <td>70.4252</td>
      <td>0.1146</td>
      <td>0.1146</td>
      <td>1.0252</td>
      <td>69.7417</td>
      <td>20.0417</td>
      <td>0.1378</td>
      <td>7320</td>
      <td>0.5126</td>
      <td>20.7252</td>
      <td>19.7</td>
      <td>20.2126</td>
      <td>-905</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-0.1838</td>
      <td>1956.29</td>
      <td>0.0019</td>
      <td>70.6</td>
      <td>-937.5</td>
      <td>46.5</td>
      <td>50.306</td>
      <td>71.5642</td>
      <td>0.1108</td>
      <td>0.1108</td>
      <td>0.9642</td>
      <td>70.9214</td>
      <td>20.6154</td>
      <td>0.14</td>
      <td>7440</td>
      <td>0.4821</td>
      <td>21.2582</td>
      <td>20.294</td>
      <td>20.7761</td>
      <td>-937.5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-0.1914</td>
      <td>1955.98</td>
      <td>0.0019</td>
      <td>71.4</td>
      <td>-968</td>
      <td>47.2</td>
      <td>50.913</td>
      <td>72.3933</td>
      <td>0.1108</td>
      <td>0.1108</td>
      <td>0.9933</td>
      <td>71.7311</td>
      <td>20.8181</td>
      <td>0.1416</td>
      <td>7560</td>
      <td>0.4967</td>
      <td>21.4803</td>
      <td>20.487</td>
      <td>20.9837</td>
      <td>-968</td>
    </tr>
    <tr>
      <th>4</th>
      <td>-0.203</td>
      <td>1955.53</td>
      <td>0.0018</td>
      <td>72.6</td>
      <td>-999.5</td>
      <td>47.9</td>
      <td>51.67</td>
      <td>73.535</td>
      <td>0.104</td>
      <td>0.104</td>
      <td>0.935</td>
      <td>72.9117</td>
      <td>21.2417</td>
      <td>0.1438</td>
      <td>7680</td>
      <td>0.4675</td>
      <td>21.865</td>
      <td>20.93</td>
      <td>21.3975</td>
      <td>-999.5</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>446</th>
      <td>-0.0836</td>
      <td>1960.21</td>
      <td>0.0493</td>
      <td>136.8</td>
      <td>-4287.5</td>
      <td>90.1</td>
      <td>90.763</td>
      <td>161.972</td>
      <td>2.0199</td>
      <td>2.0199</td>
      <td>25.1722</td>
      <td>145.191</td>
      <td>54.4277</td>
      <td>0.3175</td>
      <td>60720</td>
      <td>12.5861</td>
      <td>71.2092</td>
      <td>46.037</td>
      <td>58.6231</td>
      <td>-4287.5</td>
    </tr>
    <tr>
      <th>447</th>
      <td>-0.0836</td>
      <td>1960.21</td>
      <td>0.0495</td>
      <td>136.8</td>
      <td>-4287.5</td>
      <td>90.2</td>
      <td>90.763</td>
      <td>162.074</td>
      <td>2.0199</td>
      <td>2.0199</td>
      <td>25.2742</td>
      <td>145.225</td>
      <td>54.4617</td>
      <td>0.3177</td>
      <td>60840</td>
      <td>12.6371</td>
      <td>71.3112</td>
      <td>46.037</td>
      <td>58.6741</td>
      <td>-4287.5</td>
    </tr>
    <tr>
      <th>448</th>
      <td>-0.0838</td>
      <td>1960.21</td>
      <td>0.0494</td>
      <td>136.8</td>
      <td>-4288</td>
      <td>90</td>
      <td>90.763</td>
      <td>162.024</td>
      <td>2.0199</td>
      <td>2.0199</td>
      <td>25.2236</td>
      <td>145.208</td>
      <td>54.4449</td>
      <td>0.3176</td>
      <td>60960</td>
      <td>12.6118</td>
      <td>71.2606</td>
      <td>46.037</td>
      <td>58.6488</td>
      <td>-4288</td>
    </tr>
    <tr>
      <th>449</th>
      <td>-0.0834</td>
      <td>1960.22</td>
      <td>0.0493</td>
      <td>136.8</td>
      <td>-4288</td>
      <td>90.2</td>
      <td>90.763</td>
      <td>161.971</td>
      <td>2.0206</td>
      <td>2.0206</td>
      <td>25.1714</td>
      <td>145.19</td>
      <td>54.4275</td>
      <td>0.3175</td>
      <td>61080</td>
      <td>12.5857</td>
      <td>71.2084</td>
      <td>46.037</td>
      <td>58.6227</td>
      <td>-4288</td>
    </tr>
    <tr>
      <th>450</th>
      <td>-0.0834</td>
      <td>1960.22</td>
      <td>0.0493</td>
      <td>136.8</td>
      <td>-4288</td>
      <td>90</td>
      <td>90.763</td>
      <td>161.971</td>
      <td>2.0206</td>
      <td>2.0206</td>
      <td>25.1714</td>
      <td>145.19</td>
      <td>54.4275</td>
      <td>0.3175</td>
      <td>61200</td>
      <td>12.5857</td>
      <td>71.2084</td>
      <td>46.037</td>
      <td>58.6227</td>
      <td>-4288</td>
    </tr>
  </tbody>
</table>
<p>451 rows × 20 columns</p>
</div>




```python
dfshear
```




<div>
    
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Radial Strain</th>
      <th>Current Area</th>
      <th>Load Cell</th>
      <th>Radial Pressure</th>
      <th>Back Volume</th>
      <th>Back Pressure</th>
      <th>Pore Pressure</th>
      <th>Axial Stress</th>
      <th>Axial Strain</th>
      <th>Axial Displacement</th>
      <th>Deviator Stress</th>
      <th>Cambridge p</th>
      <th>Eff, Cambridge p'</th>
      <th>Axial Force</th>
      <th>Time since start of test</th>
      <th>Max Shear Stress t</th>
      <th>Eff, Axial Stress</th>
      <th>Eff, Radial Stress</th>
      <th>Mean Stress s</th>
      <th>Volume Change</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>451</th>
      <td>-0.083</td>
      <td>1960.24</td>
      <td>0.0495</td>
      <td>136.8</td>
      <td>-4288</td>
      <td>90.2</td>
      <td>90.763</td>
      <td>162.072</td>
      <td>2.0214</td>
      <td>2.0214</td>
      <td>25.2722</td>
      <td>145.224</td>
      <td>54.4611</td>
      <td>0.3177</td>
      <td>61320</td>
      <td>12.6361</td>
      <td>71.3092</td>
      <td>46.037</td>
      <td>58.6731</td>
      <td>-4288</td>
    </tr>
    <tr>
      <th>452</th>
      <td>-0.083</td>
      <td>1960.23</td>
      <td>0.0495</td>
      <td>136.8</td>
      <td>-4288.5</td>
      <td>90.2</td>
      <td>90.763</td>
      <td>162.073</td>
      <td>2.0214</td>
      <td>2.0214</td>
      <td>25.2726</td>
      <td>145.224</td>
      <td>54.4612</td>
      <td>0.3177</td>
      <td>61440</td>
      <td>12.6363</td>
      <td>71.3096</td>
      <td>46.037</td>
      <td>58.6733</td>
      <td>-4288.5</td>
    </tr>
    <tr>
      <th>453</th>
      <td>-0.0828</td>
      <td>1960.24</td>
      <td>0.0494</td>
      <td>136.8</td>
      <td>-4289</td>
      <td>90.2</td>
      <td>90.763</td>
      <td>162.021</td>
      <td>2.0222</td>
      <td>2.0222</td>
      <td>25.2207</td>
      <td>145.207</td>
      <td>54.4439</td>
      <td>0.3176</td>
      <td>61560</td>
      <td>12.6104</td>
      <td>71.2577</td>
      <td>46.037</td>
      <td>58.6474</td>
      <td>-4289</td>
    </tr>
    <tr>
      <th>454</th>
      <td>-0.0828</td>
      <td>1960.24</td>
      <td>0.0494</td>
      <td>136.8</td>
      <td>-4289</td>
      <td>90.2</td>
      <td>90.763</td>
      <td>162.021</td>
      <td>2.0222</td>
      <td>2.0222</td>
      <td>25.2207</td>
      <td>145.207</td>
      <td>54.4439</td>
      <td>0.3176</td>
      <td>61680</td>
      <td>12.6104</td>
      <td>71.2577</td>
      <td>46.037</td>
      <td>58.6474</td>
      <td>-4289</td>
    </tr>
    <tr>
      <th>455</th>
      <td>-0.0824</td>
      <td>1960.26</td>
      <td>0.0494</td>
      <td>136.8</td>
      <td>-4289</td>
      <td>90.2</td>
      <td>90.763</td>
      <td>162.019</td>
      <td>2.0229</td>
      <td>2.0229</td>
      <td>25.2195</td>
      <td>145.207</td>
      <td>54.4435</td>
      <td>0.3176</td>
      <td>61800</td>
      <td>12.6098</td>
      <td>71.2565</td>
      <td>46.037</td>
      <td>58.6468</td>
      <td>-4289</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1203</th>
      <td>7.21</td>
      <td>2256.84</td>
      <td>0.0947</td>
      <td>137.2</td>
      <td>-4323.5</td>
      <td>88.4</td>
      <td>123.493</td>
      <td>179.144</td>
      <td>14.9138</td>
      <td>14.9138</td>
      <td>41.9443</td>
      <td>151.181</td>
      <td>27.6884</td>
      <td>0.4043</td>
      <td>151560</td>
      <td>20.9722</td>
      <td>55.6513</td>
      <td>13.707</td>
      <td>34.6792</td>
      <td>-4323.5</td>
    </tr>
    <tr>
      <th>1204</th>
      <td>7.2226</td>
      <td>2257.36</td>
      <td>0.0953</td>
      <td>137.2</td>
      <td>-4323.5</td>
      <td>88.6</td>
      <td>123.493</td>
      <td>179.413</td>
      <td>14.9336</td>
      <td>14.9336</td>
      <td>42.2128</td>
      <td>151.271</td>
      <td>27.7779</td>
      <td>0.405</td>
      <td>151680</td>
      <td>21.1064</td>
      <td>55.9198</td>
      <td>13.707</td>
      <td>34.8134</td>
      <td>-4323.5</td>
    </tr>
    <tr>
      <th>1205</th>
      <td>7.2354</td>
      <td>2257.91</td>
      <td>0.0954</td>
      <td>137.2</td>
      <td>-4323.5</td>
      <td>88.6</td>
      <td>123.493</td>
      <td>179.458</td>
      <td>14.9541</td>
      <td>14.9541</td>
      <td>42.2581</td>
      <td>151.286</td>
      <td>27.793</td>
      <td>0.4052</td>
      <td>151800</td>
      <td>21.1291</td>
      <td>55.9651</td>
      <td>13.707</td>
      <td>34.8361</td>
      <td>-4323.5</td>
    </tr>
    <tr>
      <th>1206</th>
      <td>7.2484</td>
      <td>2258.45</td>
      <td>0.0948</td>
      <td>137.2</td>
      <td>-4323.5</td>
      <td>88.6</td>
      <td>123.341</td>
      <td>179.194</td>
      <td>14.9746</td>
      <td>14.9746</td>
      <td>41.9935</td>
      <td>151.198</td>
      <td>27.8568</td>
      <td>0.4047</td>
      <td>151920</td>
      <td>20.9968</td>
      <td>55.8525</td>
      <td>13.859</td>
      <td>34.8558</td>
      <td>-4323.5</td>
    </tr>
    <tr>
      <th>1207</th>
      <td>7.2604</td>
      <td>2258.96</td>
      <td>0.0943</td>
      <td>137.2</td>
      <td>-4323.5</td>
      <td>88.8</td>
      <td>123.493</td>
      <td>178.932</td>
      <td>14.9936</td>
      <td>14.9936</td>
      <td>41.7321</td>
      <td>151.111</td>
      <td>27.6177</td>
      <td>0.4042</td>
      <td>152040</td>
      <td>20.8661</td>
      <td>55.4391</td>
      <td>13.707</td>
      <td>34.5731</td>
      <td>-4323.5</td>
    </tr>
  </tbody>
</table>
<p>757 rows × 20 columns</p>
</div>



# Whole test and shearing graphs

As seen below p' vs q graphs can be seen using the data from the whole test and next is one with the shearing part.


```python
fig = plt.figure(figsize=(5,3.5))
plt.plot(dftrim['Eff, Cambridge p\''],dftrim['Deviator Stress'],label='p\'-q',linewidth=0.8)
plt.legend(bbox_to_anchor=(1.02, 1), loc=2, borderaxespad=0)
plt.ylabel('q (kPa)')
plt.xlabel('p\' (kPa)' )
plt.xlim(0,)
plt.ylim(0,)
plt.grid(b=True, which='major', color='#A79E9E', linestyle='-', linewidth=0.4)
plt.minorticks_on()
plt.grid(b=True, which='minor', color='#999999', linestyle='-', alpha=0.2)
```


![](https://github.com/highjoule/triaxread/blob/main/images/displ_time_whole.png)



```python
fig = plt.figure(figsize=(5,3.5))
plt.plot(dfshear['Eff, Cambridge p\''],dfshear['Deviator Stress'],label='p\'-q',linewidth=0.8)
plt.legend(bbox_to_anchor=(1.02, 1), loc=2, borderaxespad=0)
plt.ylabel('q (kPa)')
plt.xlabel('p\' (kPa)' )
plt.xlim(0,)
plt.ylim(0,)
plt.grid(b=True, which='major', color='#A79E9E', linestyle='-', linewidth=0.4)
plt.minorticks_on()
plt.grid(b=True, which='minor', color='#999999', linestyle='-', alpha=0.2)
```

![](https://github.com/highjoule/triaxread/blob/main/images/displ_time_shear.png)




