# Probability Table Generation and Next-Word Prediction

#### __The idea is very simple. There are a few steps involved in the creation of the algorithm, we can begin by creating the following `.R` files:__  

- [x] `DataLoading.R` : A script to load the data  
- [x] `DataCleaning.R` : A script to clean the data  
- [x] `NGramGenerate.R` : A script to generate Term Document Matrices based on N-Grams  
- [x] `ProbabilityDataFrame.R` : A script to generate a Probability Data Frame  
- [x] `PredictionFunction.R` : A function that takes an input text and predicts an output

---

#### __What the `PredictionFunction(InputText)` actually does:__  
  
Runs 8 distinct but related mini-functions:  

1. ##### __`f.clean(InputText)`__ 
        1.1. `f.clean` is a function that takes a phrase in the form of a string variable `InputText`.  
        1.2. Lowercases it.  
        1.3. Cleans it from non-alphanumeric symbols, while retaining spaces.  
        1.4. Outputs a variable `CleanText`.
        
2. ##### __`f.count(CleanText)`__  
        2.1. `f.count` takes the string variable `CleanText` and counts the number of words in it.
        2.2. The number of words is assigned the variable `N`
        2.3. Creates another variable `M` which is equal to `N + 1`
  
3. ##### __`f.N(M)`__   
        3.1. Takes `M` as an input.  
        3.2. Accesses data frames based on `M` value
                3.2.1. If `M` >= 6, accesses `SixGram.df`
                3.2.2. If `M` = 5, accesses `FiveGram.df`
                ...and so on...
        
4. ##### __`f.search(NGram.df)`__  
        4.1. Takes the variable generated from the step above, `NGram.df`
        4.2. Searches the dataframe for matches for `CleanText`
        4.3. Outputs results to `SearchText`
        
5. ##### __`f.sort(SearchText)`__      
        5.1. Takes the variable `SearchText`
        5.2. Sorts it by descending order of probability
        5.3. Outputs results to `SortText` 
        
6. ##### __`f.join(SortText)`__ 
        6.1. Takes the variable `SortText`
        6.2. Copies it to `JoinText` variable
        6.3. Creates a column called `$Extract` which has the last word removed
        
7. ##### __`f.select(JoinText)`__
        7.1. Takes the variable `JoinText`
        7.2. Selects matches of `CleanText` and the `$Extract` Column
        7.3. Saves the results to `SelectText`
        
8. ##### __`f.predict(SelectText)`__  
        8.1 Takes `SelectText` as an input.  
        8.2 Creates a `$Terms` and `$Probability` data frame `PredictText` from the input
        8.3 Extracts the most probable Next-Word Predictions as it iterates over each row in `PredictText`  
        
---
  
__The general guidelines are as follows:__  
- [x] Creating N-Gram Models using `RWeka`'s `NGramTokenizer` Function from `N = 2`  
to `N = 6`. Preferably a one-time operation using the entire cleaned dataset and not a `15%` sample. Then saved and loaded as a list of `.RData` files.
- [x] Convert each `tdm` to a `data.frame`
- [x] Create a Frequency/Total Probability Column for each N-Gram data.frame 

--- 
 
__Then we can define the function:__  
- [x] Takes a string of text.  
- [x] Lowercases it.  
- [x] Finds the number of words in the string  
- [x] Based on the number of words, it assigns `N = Number of Words` for upto the last `N = 6` words in the string.  
- [ ] Recursively and negatively iterates N only if no match occurs from the N-Gram Data Frame. Example: "I am here" is the string we input, we want to predict the last word "dad". So it assigns `N = 3` and searches the dataframe for a match from the `N = N + 1`, i.e `N = 3 + 1 = 4` column.   
- [ ] If a possible partial match is available, it returns it and the probabilities assigned to the top 1-5 matches.  
- [ ] If there is no match. It iterates down to `N = 2` and searches `N = 3` column for matches. So instead of using "I am here" to find "I am here dad", you use "am here" to find "am here dad".   
- [ ] If there is no match. It iterates down even further to `N = 1` and searches `N = 2` column for matches. So instead of using "am here" to find "am here dad", you use "here" to find "here dad".   
