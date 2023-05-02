---
title: Pandas
---

## Example


``` python
#== IMPORTS ==#
import pandas
import math

#== FUNCTIONS ==#
def header(lookup: str, data: dict):

    output = {}
    
    for key, value in data.items():
        if lookup in key:
            output[key] = value

    return output
    
#== VARIABLES ==#
#HEADER_FILE = input("Header file: ")
#DATA_FILE = input("Data file: ")

#-- Task 2.1
HEADER_FILE = "header.txt"
DATA_FILE = "data.gz"

#== ROUTINE ==#
#-- Task 2.2
#.. (A) initiatialization pandas DataFrame --#
df = pandas.read_csv(
    DATA_FILE,
    sep="\s+",
    header=None,
    parse_dates=[[0, 1, 2, 3]]
)

#.. (B) reading header list --#
header_list = []
with open(HEADER_FILE,"r") as file:
    for line in file.readlines():
        line_list = [str(x).rstrip().lstrip() for x in line.split(',')]
        header_list.append(line_list)


#.. (B) Error handling, missing, make sure the lists are identical in length

#.. (B) creating an dict out of those lists
header_dict = {header_list[0][i]: header_list[1][i] for i in range(len(header_list[0]))}

LABEL_DATE     = list(header("Date",header_dict).keys())[0]
LABEL_TEMP     = list(header("Temp",header_dict).keys())[0]
LABEL_DEW      = list(header("Dew",header_dict).keys())[0]
LABEL_PRESSURE = list(header("Pres",header_dict).keys())[0] 
LABEL_WIND     = list(header("Speed",header_dict).keys())[0] 
LABEL_RAIN_1H  = list(header("1h",header_dict).keys())[0] 
LABEL_RAIN_6H  = list(header("6h",header_dict).keys())[0] 

#.. (C) set DataFrame header
df.columns = list(header_dict.keys())

#-- Task 2.3
#.. set DataFrame index
df.set_index(LABEL_DATE,inplace=True)


#-- Task 3.1 
#.. replacing -9999 to be math.nan
df.replace(
    {
        -9999:math.nan
    },
    inplace=True
)

#-- Task 3.2, 3.3
#.. (A) replace wind direction
df[LABEL_WIND].replace(
    {
        0:math.nan,
        360:0
    },
    inplace=True
)

#.. (B) replace negative rain with zero
for val in df[df[LABEL_RAIN_1H] < 0.0].index:
    df.loc[val,LABEL_RAIN_1H] = 0.0

for val in df[df[LABEL_RAIN_6H] < 0.0].index:
    df.loc[val,LABEL_RAIN_6H] = 0.0

#.. check NaN statistics
print(f'Any values in {LABEL_RAIN_6H} are NaN: {df[LABEL_RAIN_6H].isna().values.any()}')
print(f'So many values of {LABEL_RAIN_6H} are NaN: {df[LABEL_RAIN_6H].isna().sum()}')
print('----------------------------')
print('Which column has NaN values:')
print(f'{df.isna().any()}')
print('---------------------------------------')
print('How many values in each column are NaN:')
print(f'{df.isna().sum()}')
print('---------------------------------------')

#-- Task 3.4
#.. drop all lines with no data
df.dropna(
    how="all", 
    axis="columns",  # or axis=1
    inplace=True
)

#-- Task 3.5
#.. (A) Devided by a factor of 10
print(df)
for name in header_dict:
    if ("Date" not in name) and ("Condition" not in name) and ("Direction" not in name):
        df[name] /= 10
#.. (B) Update pressure particular => Pressure in Pa
df[LABEL_PRESSURE] = df[LABEL_PRESSURE] * 100

print(df)

#-- Task 3.6 and 3.7
#.. (A) generating a list of all timestamps in a year
timestamp_start = "2020-01-01 00:00"
timestamp_end   = "2020-12-31 23:00"

expected_timestamps = pandas.date_range(
    start=timestamp_start, 
    end=timestamp_end, freq="H"
)

#.. (B) identify missing data and treat missing data
for timestamp in expected_timestamps:
     
    #.. (df.index == timestamp).any() , if any value is true the whole expression is True
    if not (df.index == timestamp).any():
        #.. generates a empty Series with the current timestamp as an index
        new_row = pandas.Series(name=timestamp) 
        #.. add data to the DataFrame, missing data will be filled with NaN
        df = df.append(new_row)
        print('Missing timestamp:',timestamp)



#-- Task 2.4,5,6,7,8
#-- statistics
print("------------------------")
print("-- Overall Statistics --")
print(df.describe())
print("------------------------")

#-- Tasks 4.1 and 4.2
#.. Min/Max/Mean values
print('--------------')
for topic in [LABEL_TEMP, LABEL_DEW, LABEL_WIND, LABEL_RAIN_1H, LABEL_RAIN_6H]:
    print(f'Statistics on {topic} in {header_dict[topic]}')
    print(f'Min: {df[topic].min():.1f}')
    print(f'Max: {df[topic].max():.1f}')
    print(f'Mean: {df[topic].mean():.2f}')
    print('--------------')

#-- Task 4.3
#.. total values for precipitation
for topic in [LABEL_RAIN_1H, LABEL_RAIN_6H]:
    print(f"Total precipitation measured: {df[topic].sum()} {header_dict[topic]}")

print('--------------')

#== Task 5
#-- Task 5.1 Temperature Difference Data
diff_temp = df.diff()[LABEL_TEMP] 

#-- Task 5.2 dT_max and dT_min
print(
    f"Largest temperature rise",
    f"is on {diff_temp.idxmax()}",
    f"with {diff_temp.max():.2f} in K",
    f"within an hour"
)

print(
    f"Largest temperature drop",
    f"is on {diff_temp.idxmin()}",
    f"with {diff_temp.min():.2f} in K",
    f"within an hour"
)

#-- Task 5.3
#.. create data for the NaN values

#-- Task 5.4 
#.. a nice day
#.. T > 10°C, Wind < 4m/s, Rain == 0
nice_days = df[(df[LABEL_RAIN_1H] == 0.0) & (df[LABEL_TEMP] > 10) & (df[LABEL_WIND] < 4.0)]
print(nice_days)
```

