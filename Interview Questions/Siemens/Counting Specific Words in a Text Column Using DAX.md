# **Counting Specific Words in a Text Column Using DAX**  

## **Scenario**  

You have a table (`Siemens`) with a column containing multiple rows of text values. Some rows contain the words **"Apple," "Bat," or "Cat"** (case-insensitive). The goal is to write a **DAX measure** that calculates the **count of rows** where each of these words appears.  

**Example Table**:  
| Column          |  
|-----------------|  
| Apple Bat Cat   |  
| Bad Cat Apple   |  
| Cat Apple Bat   |  
| Apple           |  
| Bat             |  
| Cat             |  

> [!NOTE]  
> This problem tests your ability to use DAX for text manipulation and conditional counting.

## **Solution Code (DAX Measure)**  

```dax  
CountWords =  
VAR _Apple =  
    CALCULATE(  
        COUNTROWS(Siemens),  
        SEARCH("Apple", Siemens[Column], 1, 0) >= 1  
    )  
VAR _Cat =  
    CALCULATE(  
        COUNTROWS(Siemens),  
        SEARCH("Cat", Siemens[Column], 1, 0) >= 1  
    )  
VAR _Bat =  
    CALCULATE(  
        COUNTROWS(Siemens),  
        SEARCH("Bat", Siemens[Column], 1, 0) >= 1  
    )  
RETURN  
    "Value of Apple = " & _Apple &  
    ", Value of Cat = " & _Cat &  
    ", Value of Bat = " & _Bat  
```  

## **Explanation of the Code**  

1. **Variables (`VAR`)**:  
   - Three variables are created to store counts for each word (`_Apple`, `_Cat`, `_Bat`).  

2. **`CALCULATE` + `COUNTROWS`**:  
   - `COUNTROWS(Siemens)` counts the rows in the `Siemens` table.  
   - `CALCULATE` modifies this count based on a filter condition.  

3. **`SEARCH` Function**:  
   - Checks if the word exists in the `Column` of the `Siemens` table.  
   - **Arguments**:  
     - `"Apple"` (or `"Cat"`, `"Bat"`): The text to search.  
     - `Siemens[Column]`: The column to search in.  
     - `1`: Starting position of the search.  
     - `0`: Case-insensitive search.  
   - Returns the position of the word if found (`>=1` means "found").  

4. **`RETURN` Statement**:  
   - Combines the results into a single string output, e.g.,  
     `"Value of Apple = 3, Value of Cat = 3, Value of Bat = 3"`.  

> [!TIP]  
> `SEARCH` is case-insensitive, making it ideal for text analysis where case doesn’t matter.  

## **Alternative Solution Using `SUMX`**  

The video mentions solving this with `SUMX`. Here’s how:  

```dax  
CountWords_SUMX =  
VAR Words = {"Apple", "Cat", "Bat"}  
RETURN  
    SUMX(  
        Words,  
        VAR CurrentWord = [Value]  // Corrected from CurrentValue(Words) to [Value]  
        RETURN  
            COUNTROWS(  
                FILTER(  
                    Siemens,  
                    SEARCH(CurrentWord, Siemens[Column], 1, 0) >= 1  
                )  
            )  
    )  
```  

**Explanation**:  
- Iterates over the list `{"Apple", "Cat", "Bat"}` using `SUMX`.  
- For each word, filters the table and counts rows where the word appears.  
- Returns the **total count across all words** (adjustable to match the original output format).  

> [!IMPORTANT]  
> Use `SUMX` when you need to iterate over a list and aggregate results.  

## **Key Takeaways**  

- **`SEARCH` is case-insensitive**, ideal for text analysis.  
- **Variables improve readability** and performance in DAX.  
- **`CALCULATE` + filters** dynamically modify aggregations.  

This document provides a clear, step-by-step explanation of counting specific words in a text column using DAX, addressing common interview questions and challenges.  

> [!TIP]  
> Practice this pattern with different datasets to reinforce your understanding of text manipulation and conditional counting in DAX.  
