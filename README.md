# Westside Brothers CSV Sample Data Generator

This ruby script was created to automatically generate CSV files with sane test data for the Westside Brothers MySQL database in the Australian Institute of ICT's Diploma of Website Development (ICT50615) Build a database (ICTDBS412) module (Assessment 2).

The script utilizes The Movie Database API to generate a list of videos, and the Faker gem to create customers, transaction/requests and payments.

Westside brothers decide Kingaroy (in Queensland) needs an online video hire shop. The data generated by this script is relative to Kingaroy.

## API Key

Get your own API key from [The Movie Database](https://www.themoviedb.org/account).

You must register providing all your details (name, address, phone, email) and then create an application. I selected 'educational', and for the application description provided the following:

> "Populate a local database for a video store with sample data from The Movie Database for educational purposes."

You should have access to your API Key immediately.

## Installation

You should be familiar with ruby and have it installed along with rubygems in order to make use of this script.

Install the prerequisite dependencies:

```bash
gem install activesupport
gem install faker
gem install themoviedb-api
```

The CSV generator script will look for your API key in the `TMDB_API_KEY` environment variable:

```bash
export TMDB_API_KEY=xxxxxxxxxxxxxxxxxxx
```

To ensure the variable is present, check for it in the list returned by:

```bash
ENV
```

To put it somewhere more permanent include the export command (above) in your `.bash_profile`.

## Compatibility

Developed with ruby 2.3.1p112 (2016-04-26 revision 54768) [x86_64-darwin15] using the following ruby gems:
- activesupport 5.0.0
- faker 1.6.6
- themoviedb-api 1.1.0

## How To Use

Clone the repository from GitHub, and run the `generate` ruby script:

```
git clone https://github.com/JurgenJocubeit/westside_bros .
cd westside_bros
ruby generate.rb
```

## Output

This script will generate 5 CSV files in the same directory in which the script is executed.

The script will also print something similar to the following in the console on completion:

```bash
-------------------------------------------------------
Westside Brothers Video Store Sample Data
-------------------------------------------------------
Videos per genre:
  * Action: 20 videos
  * Adventure: 9 videos
  * Comedy: 13 videos
  * Crime: 17 videos
  * Drama: 13 videos
  * Epics: 19 videos
  * Fantasy: 13 videos
  * Horror: 18 videos
  * Musical: 17 videos
  * Sci-Fi: 5 videos
  * War: 15 videos
  * Westerns: 18 videos
-------------------------------------------------------
Added 177 Videos (video.csv)
Added 50 Customers (customer.csv)
Added 10 Transactions (transaction.csv)
Added 10 Transaction Requests (transaction_request.csv)
Added 10 Payments (payment.csv)
-------------------------------------------------------
```

### video.csv

The script will download 20 titles for each genre, but only movies with 10 or more votes are included.

The script will de-duplicate the movies based on their id, because a movie can appear in several genres. When I ran this script I started with 240 titles, but this were reduced to 177 when the duplicates were removed.

Field | Data Type | Comment
--- | --- | ---
Video ID | Integer | Primary key
Title | String | Title from The Movie Database API for this movie
Description | String | Overview from The Movie Database API for this movie
Artist Name | String | Random name from Faker::Name
Producer Name | String | Random name from Faker::Name
Year Released | Integer | 4 digit year e.g. 2016  from The Movie Database API for this movie
Price | Integer | Random number between 1 and 12
Total Bluray | TinyInt | Random number between 1 and 25
Total DVD | TinyInt | Random number between 1 and 25
Bluray On Hand | TinyInt | Random number between 1 and 4
DVD On Hand | TinyInt | Random number between 1 and 4
Genre | String | Random from Action, Adventure, Comedy, Crime, Drama, Epics, Fantasy, Horror, Musical, Sci-Fi, War or Westerns
Classification | String | Random from G, PG, M, MA15+ or R18+

### customer.csv

The script will create 50 customers. The data is created  using a combination of Faker generators (names, street address and email address domain); and random data relative to Kingroy (such as telephone area prefix and a random selection of nearby suburbs).

Field | Data Type | Comment
--- | --- | ---
Customer ID | Integer | Primary key
First Name | String |
Last Name | String |
Street Address | String |
Suburb | String | Random from Kingaroy, Gordonbrook, Taabinga, Coolabunia or Booie
State | String | Always Queensland
Postcode | Integer | Always 4610
Contact Number | Integer | Random telephone number relative to Kingaroy
Email Address | String | Random free email service using First Name (above) and random number
Date Joined | Date | Random date in the last year using the format YYYY-MM-DD

### payment.csv

The script will create 10 payments. These records will use a sample of 10 videos so that the payment amount matches the video price, and a sample of 10 customers so that the payment date occurs after the customer date joined.

Field | Data Type | Comment
--- | --- | ---
Payment ID | Integer | Primary key
Payment Method | String | Random from Cash, EFTPOS or Credit Card
Hire Amount | Decimal (5,2) | 90% of Video Price from sample of 10 videos
Shipping Amount | Decimal (5,2) | Defaults to 0.00
Tax Amount | Decimal (5,2) | 10% of Video Price from sample of 10 videos
Payment Amount | Integer | Matches (100%) Video Price from sample of 10 videos
Date Paid | Date | Random date after Customer Date Joined using the format YYYY-MM-DD

### transaction.csv

The script will create 10 transactions. These transactions will use a sample of 10 customers (from above). This is to ensure the transaction date rented and transaction date due occur after the customer date joined.

Field | Data Type | Comment
--- | --- | ---
Transaction ID | Integer | Primary key
Customer ID | Integer | Foreign key, matches Customer ID from sample of 10 customers
Payment ID | Integer | Foreign key
Date Rented | Date | Matches Payment Date Paid (from above) using the format YYYY-MM-DD
Date Due | Date | 7 days after Date Rented (above) using the format YYYY-MM-DD
Date Dispatched | Date | Defaults to Date Rented above
Date Returned | Date | Defaults to Date Due above
Pick Up Delivery | String | Random from 'pick-up' or 'delivery'
Transaction Note | String | Random sentence from Faker::Hipster.sentence

### transaction_request.csv

The script will create 10 transaction requests. These transaction requests will use the same sample of 10 videos (from above). This is to ensure the Video ID matches Payment Amount. The Transaction ID matches the transaction record from above.

Field | Data Type | Comment
--- | --- | ---
Transaction Request ID | Integer | Primary key
Transaction ID | Integer | Foreign key, matches Transaction ID from above
Video ID | Integer | Foreign key, matches Video ID from sample of 10 videos
Video Type | String | Random from DVD or Blu-ray
Request Note | String | Random sentence from Faker::Hipster.sentence

## Notes

The CSV files do not contain column headers. Any string (text) values that contain a comma are encapsulated in double-quote marks (as per the CSV spec).

The script will delete existing CSV files before attempting to write them. If you want to keep existing files, then you should rename them.

## Credit

This script was created by Jurgen Jocubeit:
- [Twitter](https://twitter.com/jurgenjocubeit) @jurgenjocubeit
- [GitHub](https://github.com/JurgenJocubeit) JurgenJocubeit

## License

This repository is released in the public domain under the [MIT License](http://opensource.org/licenses/MIT).

## Copyright

Copyright 2016 Jurgen Jocubeit.
All rights reserved.
