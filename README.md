# Data Merging (Algorithmic) Challenge

## Running the Program

To execute the main program, run:
```
python -m src.app -parsemode
```
In the top directory (```.\merging_challenge```).
Where ```-parsemode``` argument must be provided: ```'l'``` for line-by-line parsing of JSON data or ```'b'``` for batch parsing.
The output JSON file is written in ```.\merging_challenge\dat```, where the given data files also lie.

## Design Choices

The main module ```app.py``` lies in the ```\src``` directory while modules for extracting and writing JSON data lies in the ```\src\data_utils```. 

The challenge was done using standard python libraries ```json``` and ```sys```.

To find matching objects, the dataset must be traversed at least once to compare against already "seen" objects in the dataset. To minimise the access cost of the data structure in which to store the "seen" objects, a hash table (ie. dictonary) was used to give a constant look-up time for these objects, yielding higher run-time performance versus a list, for example. 

Once a match has been found by comparing the 3 attributes between what has been "seen" and the current object that is being compared, the category field is examined and the category with the higher score is updated into the "seen" look-up table, effectively keeping the best category by score for each instance of an object in the "seen" dictionary. A trade-off here is in space complexity, as up to n(length of dataset) size dictionary has to be used for the storage of the "seen" dictionary. 

To minimize I/O costs, each object was compared in the order title, city and country, respectively. Assuming that the probability of the title of the place matching is much lower than that of the city or country attribute matching, comparing the attributes with the highest specificity first reduces the number of objects examined to get a match across all three attributes. 

Initially, the dataset was loaded in batch (```'b'``` mode) into a variable within the program and that variable was iterated through merge the dataset. This requires a considerable RAM as the entirety of the dataset is loaded in-page before matching and merging. This approach can also scale inefficiently since the RAM demand will scale with the size of the input dataset. 

To address this and to make the program as RAM-efficient as possible, the dataset was loaded line-by-line (```'l'``` mode). The JSON Line format allows this as the file contains individual JSON objects separated by line breaks, allowing each object to be loaded and processed individually rather than in batch. To furthur minimize I/O costs, the "seen" dictionary is passed by reference each time and updated rather than copied. 

## Runtime and Cost Estimates

The runtime of each mode was estimated with the time difference using the ```time``` module. The batch approach took on average (10 runs) **2.12** seconds while the line-by-line method took on average **1.62** seconds. (about **24%** improvement)

The amount of RAM needed (line-by-line) would be O(n\*s), where s is the size of each object(ie. in bytes). The I/O cost would be O( (n\*u)/p ) objects, where u is the ratio of the dataset where is elements are unique (ie. 1-Pr(duplicate entries are in dataset) ), and p is the size of the page. 

## Suggestion for Improvement

Assuming that the dataset has more unique entries than matching ones, it would be more efficient to delete elements from the original input data than to build the output from an empty data structure. This would reduce the space cost of the program. 
