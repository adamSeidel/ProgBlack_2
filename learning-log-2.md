# Learning Log 2
![Ready Trader Go](/assets/images/ready-trader-go.jpg)

## Changes to initial goals
- Due to time constraints and the complexity of this competiton we have decided to not submit code to the competiton. Instead we will be aiming to produce a strategy that is profitable for the four different sets of market data provided by Optiver for testing and development. We will be judging our strategy based on its profit, generated when ran inside the autotrader system locally. This will give us more time to develop and optimise our strategy without the short one week time scale imposed by the competition.

## Current goals
- Implement our basic strategy described previously into the Ready Trader Go system, so that we have buy and sell signals for the ETF and Future.
- Use these buy and sell signals to trade the order book of the ETF and the Future.

## What have you done
### Implementing Buy and Sell Signals
In order to start exectuting trades we need to have the same buy and sell signals that we produced in the Juypter analysis notebook inside the Ready Trader Go system. Unlike for our Juypter notebook implement, the data for the autotrader system will be coming in live. We will need to adapt our code so that it can calculate buy and sell signals with data coming in continuously. Once this is done further analysis will need to be undertaken to look at how long until our signals become accurate and are not skewed due to lack of data.

For the competiton all programming is done inside the autotrader.py file. This file contains an AutoTrader class which has various methods that are called when different events occur. One such method is the "on_order_book_update_message" this method is called periodically to report the status of an order book. Inside this method is where our buy and sell signals will be implemented.
![on_order_book_update_message](/assets/images/on-order-book-update-message.png)<br>
The "on_order_book_update_message" method

Each time "on_order_book_update_message" is executed the current mid point price is calculated as before, using the highest bid and lowest ask price. This value is then stored in a pandas series. Using the pandas "concat" method, the new price value is added to a series holding all the prices that have been recorded for that instrument. Using the two price series' for the Future and the ETF the ratio between each price pair is then calculated, followed by the zscore. 

We take the last zscore calculated and assign a value of -1 to a Buy signal and a value of 1 to a Sell signal. We then check if the signal has changed from buy to sell or visaversa. When there is a change the new signal is printed. Now that the autotrader has buy and sell signals trades can be made accordingly.
![buy-and-sell-signals](/assets/images/buy-and-sell-signals.png)<br>
Buy and Sell signals being printed to the console

When implemeting the buy and sell signals I discovered that some of the price ratio values were anomalous producing values such as infinity and NaN. After some investigating it was found that the first price value of both the ETF and the Future was zero. This was causing the first ratio value to always be infinity. As for the NaN this was caused by the live data. Since "on_order_book_update_message" is called each time the Future or ETF order book is updated and we add this updated price to our price series. This means that the length of the Future and ETF prices series will always differ by 1 element. This meant pandas was trying to divide a number by a value that didnt exist producing NaN. Both of these issues were fixed by removing the first value of both price series and ensuring the series were the same size.

Now that we have buy and sell signals inside the autotrader we can start to make trades. The autotrader class which holds all of our code has a method called "send_insert_order". This allows you to make trades in the order book of the ETF. It has the following parameters "client_order_id", "side", "price", "volume" and "lifespan". The "client_order_id" is generated using "next(self.order_ids)". You must then specify if you want to buy or sell the ETF using "side" along with the desired price to trade at, the volume of the trade and the type of trade. There are a few options for "lifespan" which effect how the order behaves when it is not imediately executed. The order lifespan is a potential area for later optimisation. Now using our buy and sell signals whenever a buy signal occurs the associated trade is made and likewise for when a sell signal occurs.
![orders-being-placed](/assets/images/orders-being-placed.png)<br>
Orders placed by the autotrader

The AutoTrader class has a method called "on_order_filled_message". This method ensures all trades placed by the autotrader to buy or sell the ETF are automatically headged in the Futures order book. There is the ability to change how the class hedges trades and later on in development we will have to have a look at if we can optimise this to produce greater profits. However for the time being we will leave the hedge code as is.

At this stage we have an AutoTrader class that can produce buy and sell signals for the ETF and then execute corresponding trades based on these signals. This is a realisation of the goals for this learning log to implement our trading strategry and begin exectuing trades.
![autotrader-live](/assets/images/autotrader-live.png)<br>
Autotrader in action making trades with a current net profit of $554.92

## What have you learnt
During this stage of development I become much more familiar with the pandas and numpy modules. The previous stage of development using Juypter notebooks for testing got me used to using basic series and dataframes. Due to the autotrader having to deal with live data I had to look into the best way to manage this. This lead to me becoming much more familiar with aspects of pandas such as "concat". This is a method that is used to combine two series. Which was particularly useful for adding new price values as they came in. It took me a while to get my head around which way the concatenation was done which caused some confusion about wether the latest price was at the top or bottom of the instruments price series.

Another function of pandas that I used for the first time was the "iloc" method. Unlike with conventional python arrays you cannot easily access the final element of a series. When accessing the final zscore to convert to a signal I had to use the "iloc" method which allows you to count back a specified amount of elements from the end of a series.

Alot of time in development was spent trying to get my head around the Nan and infinity results that were causing the Buy and Sell signals to be inaccurate. I learnt alot here in terms of dealing with live data and debugging effectively. When inspecting the code and its output closely all seemed fine. The correct ratio values were being generated and I could not clearly see how or why the errors were happening. It was only when I chose to dump the full price data stored for both the instruments into the console that I was able to piece together the miss match in series size and the initial 0 price values.

Throughout development for this learning log I spent alot of time and thought making sure I understood exactly what we were calculting and how these values relate to one another. As I became more comfortable with the functions of an order book and the relationship between the Future and ETF I revisited our code and began to see areas for potential improvement. I have marked these areas with a comment that contains a simple question that will lead to further thought and development when time allows.

## Next steps to achieve your goals
The next stage of this project will be the run the "autotrader.py" and assess its initial profitability. From there we will need to identify areas of possible optimisation where we can increase profits of the trader. Possible areas of improvment are the speed of the system, significance level at which a signal is triggered, type of order used, volume of order and hedge strategy. 

We will also have to run tests to ensure our autotrader stays within the rules for the competiton and also design code to deal with freak market events. Failure to stay within the rules setout by Optiver such as the volume limit and the hedge time frame will result in the autotrader being susspended.

Another area of optimisaton is how the buy and sell signals are generated. Currently every price value is stored for both the Future and the ETF along with every related ratio and zscore. There is the potential that using custom built data strucutres and algorithms can produce and store these values in a more efficient way.