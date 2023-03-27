# Learning Log 1
![Ready Trader Go](/assets/images/ready-trader-go.jpg)

## What is the Optiver Ready Trader Go Competition?
The Ready Trader Go competiton is an automated trading competition. Traders compete to design the most profitiable trading script which is written in either Python or C++. There are multiple rounds of the competiton where all the trading scripts compete together in a virtual environment with the most profitable teams progressing.

## Initial goals
- Understand rules and requirements of the competition
- Create a profitable trading strategy
- Implement trading strategy into RTG system
- Analyse profitability of our strategy

## What have you learnt?
### Watching the RTG learn videos
As part of the competiton Optiver provided 6 videos to help teams understand the requirmenets of the competiton and to become familair with the rules. The following is what I've learnt from these videos.

The videos started off with an introduction to different market instruments such as Stocks, Index funds, Futures, Options and ETFs. Whilst also explaining how these different instruments interact with each other.
![Relationship](/assets/images/market-instruments-relationship.png)<br>
Relationship between instruments

The videos also explained how there are two types of market participants, directional and non-directional. A directional market participant is buying an instrument because they think prices will go up or selling because they think prices will go down. Whereas, a non-directional market participants is avoiding exposure to market risk hence making money no matter what the market does. The competiton rules dictate that our trading strategy must be a non-directional market maker strategy.<br>
![Directional market participants](/assets/images/directional-market-participents.png)<br>
Directional market participants<br>
![Non-Directional market participants](/assets/images/non-directional-market-participants.png)<br>
Non-directional market participants

The RTG autotrader system provides us with two live order books. An order book shows us what different market participents are willing to buy and sell a specific instrument for. In the case of the competiton we will see the order book for a future and an ETF. Down the middle of an order book is a ladder showing a price that decreases by 0.01 as you go down the ladder. On the left side is buyer interest showing how many lots of the given instrument market participants are willing to buy at a given price. Whilst on the right side is the seller interest showing how many lots of the given instrument market participants are willing to sell at the given price. The distance between the buyer with the highest bid price and the seller with the lowest ask price is the bid-ask spread.<br>
![Order Book](/assets/images/order-book.png)<br>
Example Order Book

The videos also explained what a market maker is and how to be a sucessful market maker. This is what we will be required to do for the competiton. A market maker must calculate what it thinks the price of an instrument should be and then show a buy order, a little lower, and a sell order, a little higher, than its calculated price at the same. The market maker is constantly buying and selling an instument at the same time. What this does is reduces the spread, the gap between the bid and ask price, and also increases market volume, the amount of the instrument for sale and purchase. This means the market is more liquid, allowing market participants to buy and sell at a more competitive price.

The moment somebody trades with the market makers buy or sell offer you become exposed to the price of the instrument changing. To avoid this market exposure the market maker must hedge this risk with a second trade. This second trade is done on another correlated instrument on a different order book. This other instrument needs to be correlated to the origional instrument that was bought or sold. For the RTG competiton we will be market making in the order book of an ETF and hedging in the order book of a Future.

In the final videos a pairs trading strategy is explained which is a market neutral market making strategy. With this strategy you are trading two financial instrumnets that have proven to be correlated in price in the past. If the market prices of the two instrumnets deviates from the calculated relationship then a trading opportunity has occured. The instrument with the low price is bought and the instrument with the high price is sold. The idea is that the price difference will disappear and return to the correlation. When this happens the opposite trade can be done and the temporary price difference is materialised in cash profit.

### Overview of Quantitive Stratergies - SpencerPao
The user [SpencerPao](https://github.com/SpencerPao) has a very helpful [jupyter notebook](https://github.com/SpencerPao/Quantitative_Strategies/blob/main/Pairs%20Trading/Overview%20of%20Quantitative%20Strategies.ipynb) on his GitHub page. This runs through the steps of pairs trading and how to generate buy and sell signals along with checking if two instrumnets are correlated. Our plan was to use what we have learnt in the RTG learn videos and in this jupyter notebook to check if our ETF and Future are correlated and then if this is the case calculate buy and sell signals for historic data.

In the notebook SpencerPao explains that the basic idea of pairs trading is to find 2 assets that move similary with each other. Then sell the "overvalued" stock and buy the "undervalued" stock. He further explains that a common signal to use is the price ratio of the two instruments. If this ratio changes significantly then its time to trade. He suggests measuring significance with standard deviation.
![Pairs trading example](/assets/images/pairs-trading-example.png)<br>
Example of pairs trading from [Overview of quantitative stratergies](https://github.com/SpencerPao/Quantitative_Strategies/blob/main/Pairs%20Trading/Overview%20of%20Quantitative%20Strategies.ipynb)

To test for correlation SpencerPao uses the [pandas module corr method](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.corr.html) which uses the [pearson correlaton coefficient](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient) to calulate the correlation of two instrumnets. With this analysis it is determined the two stocks BRK and MSFT are corrolated.<br>
![Price Ratio between BRK and MSFT](/assets/images/price-ratio-brk-msft.png)<br>
Price ratio between BRK and MSFT from [Overview of quantitative stratergies](https://github.com/SpencerPao/Quantitative_Strategies/blob/main/Pairs%20Trading/Overview%20of%20Quantitative%20Strategies.ipynb)

Once it is determined two instrumnets are correlated he calculates the price ratio between the two instruments and then the Z score of the ratio of the two instruments. This then allows him to plot a 5 day ratio moving average, a 20 day ratio moving average and ratio graph. Finally, buy and sell signals are produced when the rolling ratio z-score moves away by 1 standard deviation.
![Buy and Sell Signals](/assets/images/buy-sell-demo-signals.png)<br>
Buy and sell signals for BRK and MSFT from [Overview of quantitative stratergies](https://github.com/SpencerPao/Quantitative_Strategies/blob/main/Pairs%20Trading/Overview%20of%20Quantitative%20Strategies.ipynb)

## What have you done?
As part of the RTG competition Optiver have provided an autotrader system which allows you to test your autotrader script during development. Included with this is 4 sets of demo order book data which can be used to train your script. Our first challenge was to convert these buy and sell orders into a live price log which we could then analyse.

To do this I edited the backend of the demo autotraders order_book.py file so that the midpoint_price method which is called by the autotrader when the live price of an instrument is needed would log the instrument and current price to a text file. This would give us a price file that we could analyse and develop our stratergy with. The changes made are marked by the commented lines.
![midpoint_price change](/assets/images/midpoint-price-change.png)<br>
Changes made to midpoint_price method

With this new price data we were able to plot the price of the ETF and the Future in a jupyter notebook. This data was across a time period of 15 minutes as given to us by the competition. I produced two plots for the different instruments as shown below.<br>
![ETF Price Plot - Market Data 1](/assets/images/etf-price.png)<br>
ETF price plot - Market Data 1<br>
![Future Price Plot - Market Data 1](/assets/images/future-plot.png)<br>
Future Price Plot - Market Data 1

With this data I calculated the pearson correlation coefficient. This showed a strong correlation between the Future and the ETF price of 0.988704. This validated the fact that we would be able to do pairs traiding at least for the first set of market data.<br>
![Future vs ETF Plot](/assets/images/future-vs-etf.png)<br>
Future vs ETF Price Plot

![Correlation](/assets/images/correlation.png)<br>
Correlation - shown in the top right and bottom left cells

With the data from Market Data 1 my partner in this collaborative project went on to complete the jupyter notebook by plotting the price ratio, z-score, 5 tick and 20 tick ratio moving averages before finally plotting the buy and sell singals when the z-score moves outside of 1 standard deviaton.<br>
![Market Data 1 Buy and Sell Signals](/assets/images/buy-sell-signals-market-data-1.png)<br>
Market Data 1 Buy and Sell Signals

## Next Steps to achieve your goals
Moving forward we will aim to implement these buy and sell signals into the RTG autotrader script. Once these buy and sell signals have been implemented we can begin to trade and hedge these signals. This will require us to understand how to place and hedge trades in the RTG system. Once this has been implmeneted we will begin a process of analysis to increase profits and reduce loss making trades. Work will also need to be taken to ensure our stratergy works across all the different sets of provided market data.