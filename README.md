&nbsp; 
&nbsp; 

## **Comparing runtimes between Merge Sort and Insertion Sort**

The purpose of this excercise to is understand the difference between the two different sorting algorithms. The following steps will be followed:

- The algorithms themselves will be evaluate to understand why their is a difference between them and a hypothesis will be formed.
- Both algorithms will sort .txt files containing 1k, 10k, 100k, 250k, 500k, and 1M unosrted integers.
- The execution time will be recorded using the 'timeit' library.
- The reuslts will be displayed in both table and graph formats (matplotlib)
- The results will be compared to my original hypothesis to form a conclusion.



First. Lets import our libraries, then take a look at the algorithms.

```python
{
  import timeit
  import matplotlib.pyplot as plt
  from tabulate import tabulate
} 
```
   
   
&nbsp;

#### **Insertion Sort**

In an usnorted array, insertion sort works by taking an element ( List[index] ) in the array and iterating through the array while comparing the List[index] to the elements. Once an element is found to be greater than List[index], then the List[index] is placed before it. Usually, the sorting process starts with selecting the second element of the list (index = 1).

It would be: 
- Iterate through the array comparing Array[index] to each element, until Array[index] < element 
- Placing it before the first element grater than it.
- Moving the index to the following position (index += 1). 
- Repeat the above steps with the new element, Array[index].

&nbsp;
Below is the code for the algorithm:


```python 
{
## Performs insertion sort on a passed 2d list
## Prameter: 2d List
## Returns number of comparisons as 'count'
def insertion_sort(intArray):
    count = 0
    for i in range(1, len(intArray)):
        current = intArray[i]
        j = i - 1
        while (0 <= j and current < intArray[j]):
            intArray[j + 1] = intArray[j]
            j = j - 1
            count += 1
        intArray[j + 1] = current
    return count
   }
   ```
&nbsp;

Looking at the process, I beleive that it would take a lot of comparisons to go through the whole array.
Best case time complexity would be O(n) if the array is already sorted. Worst case would be O(n<sup>2</sup>).
Space complexity would O(1) since the array is sorted in place, without the use of another structure.

&nbsp;


#### **Merge Sort**

In an usnorted array, merge sort works by dividing an array at its halfway point, then continue to divide the subarrays until we can no longer halve them. From here, the algorithm sorts the subarrays. Then, those sorted subarrays are merged with parallel sorted subarrays until the whole array is merged and sorted. This is considered a 'Divide and Conquer' method. Recursion is used to return the sorted subarrays to the next highest level subarray, before sorting and merging again.

Below is the code for the Merge Sort algorithm:

   
```python 
{
## Performs merge sort on a passed 2d list
## Prameter: 2d List
## Returns number of comparisons as 'count'
def merge_sort(intArray): 
    
    count = 0
    lenArray = len(intArray)
    if 2 > lenArray:
        return count
    
    mid = lenArray//2
    rightSide = intArray[mid : (lenArray - 1)]
    leftSide = intArray[0 : mid]

    merge_sort(rightSide)
    merge_sort(leftSide)
    
    left = 0
    right = 0
    index = 0   
    
    while left < len(leftSide) and right < len(rightSide):
        if leftSide[left] < rightSide[right]: intArray[index] = leftSide[left]; left += 1;
        else: intArray[index] = rightSide[right]; right += 1;
        index += 1
        count += 1
    while left < len(leftSide):
        intArray[index] = leftSide[left]
        left += 1
        index += 1
    while right < len(rightSide):
        intArray[index] = rightSide[right]
        right += 1
        index += 1
    return count
   
   
   }
   ```
&nbsp;

Looking at the process, I beleive that it would be much quicker than insertions sort, despite having a higher space complexity. Space complexity would O(n) since the array has to be divded into 'n' subarrays.
I beleive it would be quicker because merge sort follows a near linear path: (n * log(n)). 

Best case time complexity would be O(n log(n)) if the array is already sorted, since it still has to divide every subarray. On the bright side, worst case would also be O(n log(n)).


The rest of the program code is below and does the following:
- Opens 6 .txt files with 1k, 10k, 100k, 250k, 500k, and 1M unsorted integers.
- Create tables for insertion sort and merge sort results
- For each file
    - Creates unnsortedArray[n] 
    - Call each sorting algorithm
    - Check time efficiency of each sorting algorithm
    - Counts # of comparisons for each sorting algorithm
    - Appends [# Integers, # Comparisons, Time (ms)] to the respective results table (insertionTable, mergeTable)
- Prints results from each table
- Displays graph of results with matplotlib

```python
{
## Higher order function which computes processing time of function that was passed in
## Prameter: functionName , args
## Returns: List
def timeEfficiency(funcName, intArray):
    start = timeit.default_timer()
    count = funcName(intArray)
    end = timeit.default_timer()
    time = round((end - start) * 10 ** 3, 3)
    return [len(intArray), count, time]
    
def openFileArray(fileName, intArray):
    
    with open(fileName) as file:
        for line in file:
            for word in line.split():
                intArray.append(int(word))
    file.close()
    return intArray


## Plots 2 graphs in one row
## Prameter: 2d List, funcName
## Output: Plots
def plotTimes(timeTable, funcName):
    
    amount = []
    comparisons = []
    times = []
    for i in range(0, 6):
        amount.append(timeTable[i][0])
        comparisons.append(timeTable[i][1])
        times.append(timeTable[i][2])
        
    fig, merge = plt.subplots(1,2, figsize=(12,3))    
    merge[0].plot(amount,times,'-o', color='g',label = 'Amount vs Time')
    merge[1].plot(amount,comparisons,'-o', label = 'Amount vs Comaprisons')
    merge[0].set(xlabel='Amount', ylabel='Time (ms)',
       title='{}: Amount Sorted vs Time(ms)'.format(funcName))
    merge[1].set(xlabel='Amount', ylabel='Comparisons',
       title='{}: Amount Sorted vs Comparisons'.format(funcName))
    merge[0].tick_params(direction='out', length=6, width=2,color='r')
    merge[0].grid()
    merge[0].legend()
    merge[1].tick_params(direction='out', length=6, width=2,color='r')
    merge[1].grid()
    merge[1].legend()
    plt.savefig(funcName)
    
## Plots 2 graphs in one row
## Prameter: 2d List, title (of tables)
## Output: Prints out formatted tables
def printTable(table, title):
    
    
    header = ["Number of Integers", "Number of Comparisons", "Time to execute (ms)"]
    tabled = tabulate(table, headers = header, tablefmt = 'grid')
    print("\n",title)
    print(tabled, "\n")

## Main driver for pogram
def main():

    files = ["rand1000.txt", "rand10000.txt","rand100000.txt", "rand250000.txt", "rand500000.txt", "rand1000000.txt"]
    timeTable = []
    inserTable = []
       
    for file in files:
        intArray = []
        intArray = openFileArray(file, intArray)
        timeTable.append(timeEfficiency(merge_sort, intArray))
        inserTable.append(timeEfficiency(insertion_sort, intArray))
    
    printTable(timeTable, "MERGE SORT")
    printTable(inserTable, "INSERTION SORT")
    plotTimes(timeTable, "Merge Sort")
    plotTimes(inserTable, "Insertion Sort")
    

main()
}
```
## **Results**

Based on the tables below, we can see the difference in efficiency between merge and insertion sorting algorithms.
&nbsp; 

 **MERGE SORT:**  ***O(n log(n))***

|   Number of Integers |   Number of Comparisons |   Time to execute (ms) |
| -------------------- | ----------------------- | ---------------------- |
|                1,000 |                     983 |                  1.781 |
|               10,000 |                   9,986 |                 24.965 |
|              100,000 |                  99,983 |                342.025 |
|              250,000 |                 249,955 |                838.363 |
|              500,000 |                 499,985 |                1,782.94 |
|            1,000,000 |                 999,984 |                3,563.88 |


&nbsp; 

**INSERTION SORT:**  ***O(n<sup>2</sup>)***

|   Number of Integers |   Number of Comparisons |   Time to execute (ms) |
| -------------------- | ----------------------- | ---------------------- |
|                1,000 |                 134,373 |                 22.811 |
|               10,000 |   1.432 x10<sup>7</sup> |               2,192.08 |
|              100,000 |   1.443 x10<sup>9</sup> |                243,115 |
|              250,000 |   8.537 x10<sup>9</sup> | 1.837 x10<sup>6</sup>  |
|              500,000 |   3.413 x10<sup>10</sup>|  7.414 x10<sup>6</sup> |
|            1,000,000 |  1.453 x10<sup>11</sup> |  24.237 x10<sup>6</sup>|



&nbsp; 

Merge sort follows a near linear path of growth based on O(n log(n)) while Insertion sort grows exponentionally at O(n<sup>2</sup>).
The number of comparisons also goes to show that merge sort accomplishes the same task with significantly less.  
This can clearly also be seen on the graphs below.

&nbsp; 


![Merge_Sort](https://github.com/Ismaelc78/Algorithm_Runtimes/blob/main/Merge%20Sort.png?raw=true)



&nbsp; 


![Merge_Sort](https://github.com/Ismaelc78/Algorithm_Runtimes/blob/main/Insertion%20Sort.png?raw=true)




&nbsp; 

#### **Conclusion:**
In conclusion, my hypothesis of merge sort being more efficient than insertion sort was correct.  

To sort 1 million integers, merge sort took ~3.6 seconds, while insertion sort took ~ 6.73 hours. 

That is a huge difference! Merge sort wins in efficiency.


   
   
   
