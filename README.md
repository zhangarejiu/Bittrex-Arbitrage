# Bittrex Arbitrage Bot

This is one of my cryptocurrency trading bots from a while ago.  It's also one of my less profitable bots, which is why I'm making it public.  This bot works by detecting profitable cycles in the Bittrex cryptocurrency exchange graph, where for example it might use bitcoin to buy ethereum to buy monero to buy more bitcoin, such that the final value of the purchased bitcoin is greater than the starting value.  Two of the tricky aspects of doing this are Bittrex's fee structure and minimum order sizes.  

I chose Node.js for this project because it needs perform a large volume of HTTP GET requests in as short amount of time as possible before the arbitrage opportunity disappears.  Node is great for this because its concurrency model makes it easy to fire off all of the requests simultaneously and then process them as they each come back.  Doing it this way results in orders of magnitude less time spent on network latency compared to sending all of the requests serially.

At each iteration the bot fires off a request for the price ticker of all Bittrex cryptocurrencies.  In order to save time, it also predictively requests the latest orderbook information for exchange pairs which may have profitability based on the previous iteration.  Using the ticker data, it will identify potential arbitrage cycles.  Once the candidate cycles have been identified, the bot performs numerical bisection to determine how much it can trade before its own trades alter the price enough to ruin the profit opportunity, while also taking fees into account.  If the maximally profitable trade size for a candidate arbitrade cycle has positive profitability and is also greater than the minimum trade amount imposed by Bittrex, then the bot will submit a trade order to the exchange.  The beauty of this is that it's all done concurrently on multiple arbitrage cycles without blocking for any network requests.

In practice, this bot doesn't make a significant profit due to the slowness of Bittrex's trade matching engine.  By the time a submitted trade order gets processed by Bittrex's servers, there is a very high chance that the arbitrage cycle is no longer profitable.  This can be alleviated with price forecasting to predict profitable cycles before they occur, but with working price forecasting there are better ways to make money than through arbitrage.
