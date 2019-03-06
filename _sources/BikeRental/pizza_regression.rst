Predicting Pizza Prices - Linear Regression
===========================================

Linear regression is probably one of the most widely used algorithms in data science, and many other sciences.  One of the best things about linear regression is that it allows us to learn from things that we know or observations and measurements of things we know to make predictions about new things.  These predictions might be about the likelihood of a person buying a product online or the chance that someone will default on their loan payments.  To start we are going to use an even simpler example predicting the price of pizza based on its diameter.

I have made an extensive study of the pizza places in my neighborhood and here is a table of observations of pizza diameters and their price.

======== =====
Diameter Price
======== =====
6        7
8        9
10       13
14       17.5
18       18
======== =====

Your first task is to put the data into a spreadsheet and make a scatter plot of the diameter versus the price.  What you can see pretty easily from this graph is that as the diameter of the pizza goes up, so does the price.

If you were to draw a straight line through the points that came as close as possible to all of them, it would look like this:

.. image:: Figures/pizza_best_fit.png


The orange line called the trend-line or the regression line is our best guess at a line that describes the data.  This is important because we can come up with an equation for the line that will allow us to predict the y value (price) for any given x value (diameter).  Linear regression is all about finding the best equation for the line.

How do we do that?  There are actually several different ways we can come up with the equation for the line.  We will look at two different solutions, one is a closed form equation that will work for any problem like this in just two dimensions.  The second is a solution that will allow us to generalize the idea of a best fit line to many dimensions!

Recall the equation for a line that you learned in algebra:  :math:`y = mx + b`  What we need to do is to determine values for m and b.   One way we can do that is to simply guess! And keep refining our guesses until we get to a point where we are not really getting any better.  You may think this sounds kind of stupid, but it is actually a pretty fundamental part of many machine learning algorithms.  You may also be wondering how we decide what does it mean to "get better"?  In the case of our pizza problem we have some data to work with, and so for a given guess for m and b we can compare the calculated y against the known value of y and measure our error.  For example:  Suppose we guess that b = 5 and that m = .8 for a diameter of 10 we get y = .7 x 10 + 5 or 12.  Checking against our table the value should be 13 so our error is our known value minus our predicted value 13-12 or 1.  If we try the same thing for a diameter of 8 we get y = .7 x 8 + 5 or 10.6  The error here is 9 - 10.6 or -1.6.

Add a column to the spreadsheet that contains the predicted price for the pizza using the diameter as the x value and using a slope of .7 and intercept of 5.

Now plot the original set of data along with the this new table of data.  Make the original one color and your calculated table another color.  Experiment with some different guesses for the slope and intercept to see how it works.

Next lets add another column to the table where we include the error. Now we have our 'predicted values' and a bunch of error measurements.  One common way we combine these error measurements together is to compute the Mean Squared Error (MSE)  This is easy to compute because all we have to do is square each of our errors, add them up and then divide by the number of error terms we have.  Why do we square them first?  Well, did you notice that in our example one of the errors was positive and one was negative, but when we add together both positive and negative numbers they tend to cancel each other out making our final mean value smaller.  So we square them to be sure they are all positive.  We call this calculation of the MSE an objective function. In many machine learning algorithms our goal is to minimize the objective function.  That is what we want to do here, we want to find the value for m and b that minimizes the error.

The closed form solution to this problem is known to many science students.

slope = :math:`\frac{\sum{y_i - \bar{y}}}{\sum{(x_i - \bar{x})^2}`

intercept = :math:`\bar{y} - b \bar{x}`

Lets use the closed form solution to calculate values for the slope and intercept.  To do this you will need to caluclate a value for :math:`\bar{x}` and :math:`\bar{y}` that is the average value for both x and y.  You can add two columns to do the calculation of :math:`y_i - \bar{y}` and :math:`x_i - \bar{x}`

What values do you get for the slope and intercept?



To do this we will follow these steps:

1. Pick a random value for m and b
2. Compute the MSE for all our known points
3. Repeat the following steps 1000 times
   1. Make m slightly bigger and recompute the MSE does that make it smaller?  If so then use this new value for m.  If it doesn't make MSE smaller than make m slightly smaller and see if that helps.
   1. Make b slightly bigger and recompute the MSE does that make it smaller?  If so then use this new value for b and go back to step 3a.  If not then try a slightly smaller b and see if that makes the MSE smaller if so keep this value for b and go back to step 3a.
4. After repeating the above enough times we will be very close to the best possible values for m and b.  We can now use these values to make predictions for other pizzas where we know the diameter but don't know the price.


Let's develop some intuition for this whole thing by writing a function and trying to minimize the error.

You will write three functions ``compute_y(x, m, b)``, ``compute_all_y(list_of_x)`` This shoudl use ``compute_y`` and ``compute_mse(list_of_known, list_of_predictions)``

.. activecode:: act_pizza_4

Next write a function that systematically tries different values for m and b in order to minimize the MSE.  Put this function in a loop for 1000 times and see what your value is for m and b at the end.

.. activecode:: act_pizza_5


Congratulations!  You have just written your first "machine learning" algorithm.  One fun thing you can do is to save the MSE at the end of each time through the loop then plot it.  You should see the error go down pretty quickly and then level off or go down very gradually.  Note that the error will ever go to 0 because the data isn't perfectly linear.  Nothing in the real world is!

At this point your algorithms ability to 'learn' is limited by how much you change the slope and intercept values each time through the loop.  At the beginning its good to change them by a lot but as you get closer to the best answer its better to tweak them by smaller and smaller amounts. Can you adjust your code above to do this?

For two dimensional data there is even a closed form solution to this problem that one could derive using a bit of calculus.  It is worthwhile to have the students do this to see that their solution is very very close to the solution you get from a simple formula that slope = covariance / variance and intercept = avg(y) - slope * avg(x).  Write a function that will calculate the slope and intercept using this method and compare the slope and intercept with your previous error.

.. activecode:: act_pizza_6


.. raw:: html

    <iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vSi5xtRfw_mfKTMf9uOxk8UjvKGF3VikCmRy2DfFNgvd_C83oZyayF1yPUpiHvf78oonHMzW96rxynp/pubhtml?gid=0&amp;single=true&amp;widget=true&amp;headers=false" width="90%" height="300px">
    </iframe>

.. https://docs.google.com/spreadsheets/d/12_vrntk_SZq53b5w3-qxRzeJ7HoCQE6AQbXu3UeDfbY/edit?usp=sharing


