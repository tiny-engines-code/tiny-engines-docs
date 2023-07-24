< [Back](main.md)

### <font color=teal>Dimension the data</font>
- Use the nflverse site data.  It's free and has a lot of data.

- The nflverse data is delivered in swide column monolithic blocks.
- The cardinality of the data is different for many of the columns, which is great for keeping it simple, but leads to very sparse data.
- For example, a column containing the "rushing player" does not make sense for a pass play, so this data wil be null for anything but a rushing play, and there's no simple way to impute that information 
    when it is taken along with other columns with different cardinality such as game drive and downs.
    The best way to do that is to separate the data into dimensions and facts, then join them together in different ways to see what works.


### <font color=teal>Experiment one</font>
- Our first experiment will be to see if we can predict the outcome of play-calling based on the down, distance, and field position.  
- The data would be at the most granular level of a single play.  
- We'll use a simple model such as logistic regression to see if we can predict the yards gained.
- We'll also use an AutoML ensemble model to see if we can get better results.

### <font color=teal>Experiment two</font>
- We have only two weeks, so if the play-call model does not provide immediate joy (we have two weeks) we won't spend too much more time to tweak the model.  Then we'll shift to aggregate the data to the game level and see if we can predict wins and losses.
- This is not as interesting, because we don't really believe that we'll be able to predict even as well as a statistical approach, but we will still be able to see whether a model can diminish loss and increase accuracy. 


### <font color=teal>Deliverables</font>
- A proof-of-concept that shows how to format the data as input to a machine learning model.
- A proof-of-concept that shows how to train a model to predict NFL game outcomes.
- A report that shows the level-of-effort to predict NFL game outcomes using machine learning.