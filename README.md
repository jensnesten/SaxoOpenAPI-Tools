# SaxoOpenAPI Tools

## fetchprice.py 
Depending on the time horizon set, there are different limits to how much price data you can fetch in one request. Specifically for 1 minute data, you only get one day's worth of prices. To handle this limitation, you can loop through each day and month to fetch and aggregate the data incrementally:
```python
    for month in range(1, 12):
        for day in range(1, 31):
            response = get_historical_prices(headers, assetType=assetType, uic=uic, period=period, day=day, month=month)
            formatted_response = format_json(response)
            #Get all the necessary data from the response
            close_ask_values = [data['CloseAsk'] for data in formatted_response['Data']]
            time_values = [datetime.strptime(data['Time'], '%Y-%m-%dT%H:%M:%S.%fZ') for data in formatted_response['Data']]
            response = get_historical_prices(headers, assetType=assetType, uic=uic, period=period, day=day, month=month)
            formatted_response = format_json(response)
            #Get all the necessary data from the response
            close_ask_values = [data['CloseAsk'] for data in formatted_response['Data']]
            time_values = [datetime.strptime(data['Time'], '%Y-%m-%dT%H:%M:%S.%fZ') for data in formatted_response['Data']]
            high_ask_values = [data['HighAsk'] for data in formatted_response['Data']]
            low_ask_values = [data['LowAsk'] for data in formatted_response['Data']]
            open_ask_values = [data['OpenAsk'] for data in formatted_response['Data']]
            #Then we make a pandas datafram with the data, into a csv file

            new_data = pd.DataFrame({"Date": time_values, "Open": open_ask_values, "High": high_ask_values, "Low": low_ask_values, "Close": close_ask_values})
            df = pd.concat([df, new_data])
        df.to_csv("prices.csv", index=False)
```
An error will still be thrown for monthly ranges higher than 4, and from January to March due to February only being 28 days. But 3 requests instead of 365 is still pretty good.

## infoGUI.py

The UIC of each available asset can be fetched by running infoGUI.py, and choosing the desired asset type. 
<img width="1017" alt="pic" src="https://github.com/jensnesten/SaxoOpenAPI-Tools/assets/42718681/15e21390-4cf2-4c62-85a4-c91e0c9228be">

## getPriceChart.py

When you have the UIC, you can chart the price data by running getPriceChart. The charting is done using the python version of the lightweight-charts framework.
