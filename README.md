# timemachines
Time series prediction models taking the form of state machines:

- that manifest as simple pure functions ...
- where the caller is expected to maintain the state ...

These are ideal, in some respects, for lambda deployment at scale. 

### Motivation
This repo attempts to standardize a variety of disparate approaches to time series prediction around a *very* simple functional interface

    y_hat, s_after = predict(y:Union[float,[float]],           # Observed data
                        s:dict=None,                           # State
                        k:int=1,                               # Number of steps ahead to forecast
                        t:float=None,                          # Time of observation (epoch seconds)
                        a:Union(float,[float])=None,           # Variables known in advance
                        tau:int=None) -> Union(float,[float])  
    
To emphasize, every model in this collection is *just* a function and the intent is that these functions are pure. 

### Intended usage
Notice that the caller maintains state, not the "model".

    state = None
    predictions = list()
    for t,y in data.items()
        y_hat, state = predict(y,state)
        predictions.append(y_hat)
    
### Requirements on predict
Conventions:

- Expect S=None the first time, and initialize state as required
- Tau, with units in seconds, is a suggested maximum computation time
- The observation time t is epoch seconds. 
- If returning a single value:
     - This should be an estimate of y[0] if y is a vector. 
     - The elements y[1:] are to be treated as exogenous variables, not known in advance. 
     - The vector a is used to pass "known in advance" variables
- Missing data passed as np.nan
- If y=None is passed, it is a suggestion to the "model" that it has time to perform some
      offline task, such as a periodic fitting. In this case it would be typical to supply a
      larger tau than usual. 
   

### What's not in the interface
This wraps some time series prediction libraries that:

 - Use pandas dataframes
 - Bundle data with prediction logic
 - Rely on column naming conventions 
 - Require 20+ lines of setup code before a prediction can be made
 - '5min' 

Just observing, not judging. 

### Out of scope
The simple interface is not well suited to problems where exogenous data comes and goes. You might consider a dictionary interface instead, as with the river package. It is also not well suited to fixed horizon forecasting if the data isn't sampled terribly regularly. Nor is it well suited to prediction of multiple time series whose sampling occurs irregularly. 

### You may prefer
See the [list of popular time series packages](https://www.microprediction.com/blog/popular-timeseries-packages) ranked by download popularity. 

