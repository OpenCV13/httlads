Predicting Daily Bike Rentals
=============================

Lets say you are a new data scientist working for Bikes-R-Us, and your boss comes to you and asks you to develop a model for predicting bike rentals in the future.  The company is trying to decide whether to invest in more bikes so they can keep stations better stocked.  Its early in 2013 and what you have to work with is a database of rental information similar to the one with which we began this chapter.  The difference is that we have two full years of data to work with 2011 and 2012.  Because 2010 was a partial and startup year that data has been discounted as too unreliable.  You can get this data from the bikeshare_11_12.db file.

You have many questions:  How do you start?  How will you know if you are even on the right track to building a good model?  What data can I use as features when I build the model?  Should I be trying to predict rentals hour by hour? day by day?  month by month?

With big problems like this its easy to get overwhelmed with the scope of the problem, its also easy to want to make something really complex right away.  A better approach is to try something simple at first `The KISS principle <https://en.wikipedia.org/wiki/KISS_principle>`_ ,  Keep It Simple Stupid,  is a good rule to follow.  If a simple solution works why spend additional time and effort to make something complicated?

Your initial thought is that you can build a model using the date as the main feature, and you know that the database contains the information to calculate the total number of bikes rented each day.  That seems like a simple approach, but how are you going to know, much less convince your boss, that your model is any good?  You don't know what bike rentals are going to be in the future, that is what you are supposed to predict. Showing the boss your mean squared error is not likely to be too convincing as you are just confirming the data that you have used to build the model.

The Train Test Split
--------------------

The way to handle the problem of deciding whether your model is any good is to hold out some of the data that you already know.  Lets take 20% of the days that we have data for, and NOT use them in building the model.  Then we can make predictions for those days and check our work.  Its kind of like covering up the answer to a math problem so you don't cheat and just look at the answer.  We call this a training test split.  We will randomly select some data to include in building the model, and some data to leave out.  Scikit-learn even has a handy utility function that will do this for us!

To be a little more scientific about this we will split our data into a "training set" that we will use to build the model, and a "test set" that we will use to validate the model.  That is we want to test our model on data that is different from the data we used to build the model.  Testing using the data the you trained on would be like cheating as you could just build a model the memorized everything and got 100% but in the real world you need to make predictions for things that you have not seen before.

The first thing we'll do create two simplified DataFrames one that contains a column containing the date and a second column that contains the number of bikes rented on that date.  Next we'll randomly select 80% of the data to use in creating our linear regression model, and 20% of the data to use in testing.  we will use the scikit-learn ``train_test_split`` and we will end up with four data frames:

* train_X, train_y - this is the data we'll use to create our model.  Remember that in linear regression we are trying to come up with a slope and intercept value that minimize the error, so we need to know the answer.

* test_X - we will use this data (without train_y) to make predictions given a rating from test_X we'll use our model to calculate a predicted overall value.

* test_y - we will use this data along with the predicted values to come up with our score.  The score we calculate will be the mean absolute error.

By default the ``train_test_split`` function will

The ``train_test_split`` function contains an optional parameter called ``random_state`` We will use ``random_state=997`` for this parameter to ensure we are all getting the same randomness.  This will allow you to compare your results against mine and your classmates.  It will also make your results reproducible from one day to the next.

Evaluating the Model
--------------------

Now that we have the train test split, we can use the **mean squared error** on the difference between our predicted values for the test data and the known values for the test data.  This is much fairer as we are not using data that the model already "knowns" the answer for.  In addition to the mean squared error we can also compute the **mean absolute error**  This is a little nicer for us because the units of the error are the same as the what we are trying to predict.  In this case we are not trying to predict bike rentals squared, (whatever that means) but simply the number of bike rentals.  We can also compute a measure called :math:`R^2` which is a measure of how close the data are to the regression line.


Feature Engineering
-------------------

But wait, we've been making an assumption that a date is something we can just send into the LinearRegression model and it will all work.  Unfortunately that is not the case, the LinearRegression model needs to have the features represented as numbers.  In the spirit of keeping it simple, lets build our first model by simply numbering each day using 0 as the first day we have data, 1 for the second day and so on up to whatever number represents the last day we have data for.



Version 1.0 Task List
---------------------

Your task list for this project is as follows

* Read in the rental data from the database
* Transform the data into daily rental counts by resampling by day
* Number each day from 0 to N - hint sorting on the index and then resetting the index is a good starting point.
* Make the train test split of the data using the ``train_test_split`` function.
* Create a new LinearRegression model and fit the training data
* Calculate the mean squared error and mean absolute error  between the known rentals from the test data and the predicted values from the model.
* Make a graph to compare the training and test data

.. fillintheblank:: modelv1_len

   How many days of data do you have in the transformed data set (before the train test split)

   - :731: Is the correct answer
     :x: catchall feedback

.. fillintheblank:: modelv1_test_len

   How many days of data do you have in the test set?

   - :147: Is the correct answer
     :183|182: Looks like you did not set the test_size parameter to .2
     :146.2: Good calculation, but we cannot have a fraction of a row in our data.
     :x: Hint:  The correct size is between 140 and 160.  You can calculate it yourself if you got the previous question correct.  If not, you need to get that one right before doing this one.

.. fillintheblank:: modelv1_mae

   What is the mean absolute error of your predictions?

   - :1236.04.*: Is the correct answer
     :2547739.2.*: Is the mean squared error
     :x: Make sure you use the ``mean_absolute_error`` function


Compare your graph to this one after you have made it.

.. reveal:: modelv1_reg_comp
    :modal:
    :modaltitle: Predicted Versus Actual Daily Rentals V1

    .. image:: Figures/regression_compare_1.png

What do you think of the model so far?  You are probably a bit disappointed both with how the graph looks as well as the mean absolute error.  missing your daily predictions by over 900 is not great, especially in the earlier days when rentals were only around 1000.  In fact if the average daily rentals for this time period is about 2750 then you are off by around 77% every day.

We can also look at the :math:`R^2` score for this model which gives us a value of 0.42.  1.0 would be the best score possible, so we are a long way from there. But what is the interpretation of :math:`R^2`.  It tells us how much of the variation of the data is explained by the model.  Reviewing  the graph from above there is a lot of variation and our model is only accounting for 42% of it.

Lets hold off on taking this model to the boss and see if we can't refine our model to do a bit better!

Refining the Model
------------------

Don't get discouraged that our first try wasn't that great.  You might have even guessed that would be the case, textbook authors are mean that way.  Lets look at the time series of daily rentals:

.. figure:: Figures/year_one_ts.png


 The representation of the date we chose is definitely simple, but you know from the time series visualization, that numbering the days that way would leave out a lot of valuable information.  You can see in the graph above that there are seasonal variations in the data as well as variations due to the day of the week.  We also know that as we went from year to year, overall rentals kept growing.  So, instead of encoding our date as a single number lets encode the date as by adding features for year, month, day and even weekday.  Hopefully by adding more features that capture the kinds of variation we know exist will help our model.

This kind of development of the model is very common.  Start with a simple idea and then refine it, learning what you can from the previous refinements.  Sometimes an idea you think will make an improvement actually turns out to hurt more than it helps, so you have to abandon it.  This is part of the joy and the frustration of data science is that it is an experimental science.  There is not necessarily one "right way" to get the best answer.  In this particular example we might have to admit that we can only do so much.  The shape of the time series is clearly NOT a linear function, but we will do the best with the tools we have.  The truth is there are other approaches besides linear regression that will ultimately work better for data like this, but we can still learn a lot by trying to see how far we can push regression.

Version 2.0 Task List
---------------------

* Create four new features out of the date, a column for year, month, day, and weekday.  We will keep daynum as a feature as well so that we can use it to build a graph.  Later we can experiment to see if we need it at all.
* Redo the train test split using the same random_state as before
* Fit a new model using the new features.
* Make a new set of predictions for the test days
* calculate mean squared error, mean absolute error, and r2 for this new model against the known values.
* Plot the predictions against daynum to see how they look with more features

.. fillintheblank:: modelv2_mae

   What is the mean absolute error of your predictions?

   - :1043.6.*: Is the correct answer
     :2196800.74.*: Is the mean squared error
     :x: Make sure you use the ``mean_absolute_error`` function

.. fillintheblank:: modelv2_r2

   What is your r2_score for this model?

   - :0.46.*|0.459.*|.459.*|.46.*: Is the correct answer
     :incorrect: Is feedback on a specific incorrect
     :x: catchall feedback


Lets make one more refinement to our version 2.0 model.  The problem with using month number in the hopes that we will capture seasonality is that it just doesn't work.  Numbering the months from 1 to 12 implies something linear about the months.  So although there is a linear relationship in that June comes after May on the calendar for a problem where we care about seasonality it doesn't work.  The winter months include 11, 12, 1, and 2.  Probably even 3 if you live in the Midwest.

Let us do a bit more feature engineering to see if we can make an improvement.  Your first thought is probably to add a new column called season, however numbering the seasons winter=0, spring=1, summer=2, fall=3 is not really a solution.  Because there is not an ordered relationship between the seasons.  spring is not more than winter or better than winter, so assigning 0 to winter and 1 to spring is actually misleading.  There are many examples of this such as encoding gender, or location information, or marital status. The general rule is that if the data is not ordinal then don't encode it as ordinal!  But how do we encode nominal data then to make use of it in our work? The solution to this in data science is to encode this information using a technique called **one-hot encoding**.  To use one-hot encoding we'll add four new columns to our model, one for each season.  If the date for a particular row of the model is in one of the winter months then the value for winter will be 1 and the other season columns will be 0.  Similarly if the date for a row corresponds to a date in the summer then the summer column will be one and all the other season related columns will be 0.  This allows us to capture the seasonal information and use it in our model.



Version 2.5 Task List
---------------------

* Update your DataFrame to do one-hot encoding for each of the seasons.
* Retrain and retest your model


Cool!  That gave us some real improvement.

.. fillintheblank:: modelv25_mae

   What is the mean absolute error of your predictions?

   - :846.2.*: Is the correct answer
     :1633379.38.*: Is the mean squared error
     :x: Make sure you use the ``mean_absolute_error`` function


.. fillintheblank:: modelv25_r2

   What is your r2 value for this model?

   - :0.598|0.6.*|.6.*|0.59.*: Is the correct answer
     :incorrect: Is feedback on a specific incorrect
     :x: catchall feedback

After you have done so yourself, take a look and see if your graph matches this one.

.. reveal:: modelv25_viz

     .. image:: Figures/modelv25_compare.png


Version 3.0
-----------

Now that you have been through the cycle a couple of times, you are probably saying to yourself.  Hey, there are other factors to bike rental besides just the day of the week or the season!  And you are correct, it may be that the weather on a particular day is more important than the day of the week!  Who wants to rent a bike and ride through Washington DC in the rain or snow?

So, lets add some weather information.  This could be a good chance to practice your WebAPI skills again, or even do some screen scraping from a source that allows it.  But we also have some weather data for you in a table in the database.

The weather data looks like this:

* weathersit:
  - 1: Clear, Few clouds, Partly cloudy, Partly cloudy
  - 2: Mist + Cloudy, Mist + Broken clouds, Mist + Few clouds, Mist
  - 3: Light Snow, Light Rain + Thunderstorm + Scattered clouds, Light Rain + Scattered clouds
  - 4: Heavy Rain + Ice Pallets + Thunderstorm + Mist, Snow + Fog

* temp : Normalized temperature in Celsius. The values are derived via (t-t_min)/(t_max-t_min), t_min=-8, t_max=+39 (only in hourly scale)
* atemp: Normalized "feels like" temperature in Celsius. The values are derived via (t-t_min)/(t_max-t_min), t_min=-16, t_max=+50 (only in hourly scale)
* hum: Normalized humidity. The values are divided to 100 (max)
* windspeed: Normalized wind speed. The values are divided to 67 (max)

