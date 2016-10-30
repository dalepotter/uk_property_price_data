# UK House Price Data

The Land Registry publish open data on property transactions in the UK.  The datasets are publsihed in `txt` and `csv` formats but their size can make them difficult to work with.  This reposisitory contains code and guidance to create a MySQL database and table that can be used to store the data.  With this, code applications can be built using a language of your choice.

The command examples assume the use of a UNIX shell with `wget` and `mysql` already installed.


## Download the complete dataset

House price data from January 1995 is available in a single file, totalling around 3.5GB and contains over 21 million transactions (as at October 2016).   [Other downloads](https://www.gov.uk/government/statistical-data-sets/price-paid-data-downloads) are also available from the gov.uk website.

It can be downloaded using:
```
wget http://prod.publicdata.landregistry.gov.uk.s3-website-eu-west-1.amazonaws.com/pp-complete.csv
```


## Filter the data before import (optional)

As noted above, the `pp-complete.csv` file containing the entire dataset will be very large and may be difficult to work with.  One method to extract data by geographic location is to filter the csv file using regular expressions. For exmaple, the following UNIX shell command could be used to extract lines relating to Bristol area postcodes (i.e. BS0 to BS99).
```
cat pp-complete.csv | grep "BS[0-99]" > pp-data-bristol.csv
```


## Create your database schema

The SQL commands in `create_db.sql` will create a MySQL database (named `property_prices`) and table that can be used to store the data. Column names are based on the information in the [accompanying dataset guidance](https://www.gov.uk/guidance/about-the-price-paid-data).
```
cat create_db.sql | mysql -u YOUR_MYSQL_USERNAME -p  # Type your MySQL password when asked for it
```


## Import the data

The csv file containing transaction data can then be imported into your database using: 
```
mysqlimport  --fields-terminated-by=, --fields-enclosed-by=\" --local --verbose -u YOUR_MYSQL_USERNAME -p property_prices pp-complete.csv
```
