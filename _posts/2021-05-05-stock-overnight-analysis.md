---
layout: post
title: Stock Overnight Trading Ranking
usemathjax: true
---


This post will focus on picking good stocks for overnight trading. With this strategy you buy stocks in the afternoon, then sell them on the next day's morning, so I call it overnight trading to differ from day trading. 

From Googling a bit, we know stock prices fluctuate most between 14:30 and 10:30 the next day. So let's explore the strategy of buying shares at 14:30, and then selling the next day at 10:30. The goal is to maximize our profit by choosing the stocks that will have the best gains from 14:30 to 10:30. 

The end result is displayed in the table below:

![Table of Ranked Stock Data Analysis](public/images/stock_table_screenshot.png)

To see how we got there, we'll first take a look at where to get the data from.

## Acquiring Data

Before getting the stock prices for all of the tickers, we need to download all of the ticker names. I got all of the ticker names from nasdaqtrader.com. See the code below:

<script src="https://gist.github.com/jingwenlin/b406450811b312a906bb88fe4e836f1b.js"></script>

Then, it is time to download the stock prices for all of the tickers. To download the stock prices, I used the API provided by yahoo finance which can be accessed by the yf.finance package. See the code below:

<script src="https://gist.github.com/jingwenlin/6ecd4ad2a76ba37032b2ca824a9afda8.js"></script>

The downloaded raw data containing all of the time points looks like this:

<script src="https://gist.github.com/jingwenlin/e697f68c53596a99cb46147fb69b4797.js"></script>

## Preprocessing Data

Knowing stock prices fluctuate the most between 14:30 and 10:30 the next day, I selected stock prices at 14:30 and at 10:30 next day, and organized the data into a pandas dataframe indexed by date. See below:

<script src="https://gist.github.com/jingwenlin/470ff2da455e71aad4e8c4c360874277.js"></script>

## Results and Website

The data downloading is set to run everyday by the server. As shown in the first table, I used Flask to make a website to display the statistical metrics including mean profit ratio, standard deviation profit ratio and sum profit ratio of daily price differences at 14:30 and 10:30. The table lets you sort by each metric, and you can also search by ticker.

## Side Note: Predict stock price overnight differences using Machine Learning Models

I was also curious to see if I could use any machine learning models to predict the overnight price differences. To prepare the data for all of the models I will try, I converted the data into a dataframe containing 6 overnight price differences in a row. See below:

<script src="https://gist.github.com/donald-pinckney/067cc1d2f5389730bad9fa22b71bab38.js"></script>

The first 5 columns are the x columns, and the last column is the y column. This data is fed into simple linear regression, polynomial regression, and neural network regression to predict the 6th overnight price differences. Unfortunately, none of the regression models picked up any trend, and all of them were predicting the mean of overnight price differences in the training set. However, this wasn't surprising, because using one model to fit all stocks is essentially asking the model to be versatile enough to fit all different types of stocks.

So I then converted the y column to be a categorical column judging by if $$ \lvert y-\mathrm{mean}(x_1, \cdots ,x_5) \rvert > A \cdot \mathrm{std}(x_1, \cdots, x_5) $$. If the difference between y and the mean were bigger than the standard deviation, it indicates the stock is unstable. Otherwise, the stock is stable. Then, the classified data is fed into LSTM, logistic regression and many other classification models. Again, none of the models was able to learn anything. All of the models yielded a 50% accuracy on the test set. In the future I'll be looking at investigating if variations on the classification problem may yield better results.

