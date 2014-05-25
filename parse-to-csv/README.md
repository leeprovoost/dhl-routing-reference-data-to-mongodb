# Import DHL Routing Reference Data into an SQL database

Convert DHL's Routing Reference Data (pipe-delimited text files) to CSV files and import into an SQL database. You can download the source files from the [DHL Developer Centre (Routing Reference Data)](http://www.dhl.co.uk/content/gb/en/express/resource_centre/integrated_shipping_solutions/developer_download_centre1.html). This is for DHL customers that want to use the Routing Reference Data set for international postcode lookups to ensure that parcels get delivered correctly.

Download the zip file, extract the three files (country.txt, countrypc.txt and ESDv6.txt) and move them to the "source" folder in your project. Follow then the instructions below.

## prerequisites

Ensure you have Go installed and GOPATH and GOROOT environment variables set up. Read more [here](http://golang.org/doc/install).

Install the npm packages (there is a package.json file in the app root): `npm install`.

Make sure your MongoDB server is up and running: `mongod`.

Ensure you have a database named `test`. If you are using a different database name, change it in the `import-datafiles-to-mongo.sh` script.

The script will create the following collections. Change the names in the `import-datafiles-to-mongo.sh` if you want to use other names:
- intl_postcode_api_ESD
- intl_postcode_api_country
- intl_postcode_api_countrypc

Ensure that the four shell scripts have execution rights.

## country.txt

The `process-countries.sh` script reads the DHL text file `source/country.txt`  and applies the following transformations:
- Remove unused columns and only retains country code, country name, currency and postcode flag
- Convert postcode field to an actual JSON Boolean value
- Remove a couple of records that aren't actual countries

Run the script `./process-countries.sh`. It assumes that your `country.txt` file is in the `source` directory and all the output files will be placed in the `output` directory.

## countrypc.txt

Run the script `./process-countrypcs.sh`. It assumes that your `countrypc.txt` file is in the `source` directory and all the output files will be placed in the `output` directory.

## ESDv6.txt

The `process-ESD.sh` script reads the DHL text file `source/ESDv6.txt` and applies the following transformations:
- Remove the fields that we are not interested in
- Deduplicate records
- Expand postcode ranges: looks at postcode from and postcode to and creates individual records for each postcode in the range
- Ensures that all postcodes are treated as Strings instead of mix String and Int32

Run the script `./process-ESD.sh`. It assumes that your `ESDv6.txt` file is in the `source` directory and all the output files will be placed in the `output` directory.

## Import into MongoDB

Run the script `./import-datafiles-to-mongo.sh`.
