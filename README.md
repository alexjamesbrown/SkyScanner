# SkyScanner

A C# wrapper around the [SkyScanner API](http://business.skyscanner.net/portal/en-GB/Documentation/ApiOverview).

The implemented APIs:
* Flights: Live Pricing
* Currency
* Locale
* Market
* Location Autosuggest

## Installation

From the NuGet package manager console: ```Install-Package SkyScanner```

## How to use

```C#
var scanner = new Scanner(ConfigurationManager.AppSettings["apiKey"]);
var from = (await scanner.QueryLocation("London")).First();
var to = (await scanner.QueryLocation("New York")).First();

//Query flights
var flightResponse = await scanner.QueryFlight(
  new FlightQuerySettings(
    new FlightRequestSettings(from, to, new LocalDate(2015, 06, 19), new LocalDate(2015, 06, 25)),
    new FlightResponseSettings(SortType.Price, SortOrder.Ascending)));

var itinerary = flightResponse.Itineraries.First();
var estimatedPrice = itinerary.PricingOptions.Min(option => option.Price);

//Query booking
var booking = await scanner.QueryBooking(itinerary);
var actualPrice = booking.BookingOptions
  .Select(option => option.BookingItems.Sum(item => item.Price))
  .Min();
```
