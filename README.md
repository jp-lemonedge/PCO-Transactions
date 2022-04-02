# Overview

SpotImports enables the user to

1.  <u>Extract FX Rates from</u>

    -   a given source
    -   for a different period
    -   for a given list of currencies
    -   generating FXPair or FXRate
    -   using a base currency different from the source default

2.  <u>Produce file for different types of Format</u>

    1.  FIS
    2.  F2 i.e. *JPD legacy format*

3.  <u>Process the generated files</u>

    -   Execute/Upload
    -   Store in log

## Intermediate Results

An intermediate csv is always generated

## Final Results

The csv is by default transformed into an XLSX spreadsheet ready to be
processed

# Installation

## Powershell version

There is no known dependency with the version of Powershell

1.  with Powershell 5 - installed by default on W10
2.  woth Powershell 7 (tested with 7.1.5)

## Required Setup

SpotImports relies on the Import-Excel powershell module which needs to
be installed.

Reference of how to install Import-Excel can be found at:
<https://www.powershellgallery.com/packages/ImportExcel/7.0.1>

## Source-specific required

Due to the nature of the different sources and how the data is extracte
different complementary tools are required

| Source | Tool | Comments                                             |
|--------|------|------------------------------------------------------|
| ECB    | wget | Extract a file from the ECB Web site                 |
|        | 7Zip | Assumed to be part of a standard w10 installation    |
|        |      | Executables assumed to be available - Check Path     |
| FED    | curl | Extract csv file from DEF Web site                   |
| MAS    | \-   | Request relying on building an ad-hoc URL            |
|        |      | No complementary tool is required                    |
| BoE    | wget | Request with ad-hoc URL generates tab separated file |
|        |      |                                                      |

## Non Windows

The dependencies are:

-   powershell 7 (available on MacOs and various flavours of UNIX(
-   Import-Excel which can be installed as soon as Powershell is
    installed

This combination has been successfully tested on MacOS

Depending on the OS and the source the other executables on which the
import relies are usually standard wget/curl or exist even if this is
with a different name 7Zip.

Although it has not been tested, it is highly possible that the same
tool should be able to provide the same service in Non Windows-based
system.

# Directory structure

| Directory  | Comments                                                           |
|------------|--------------------------------------------------------------------|
| .          | Where the code, documentation and **Dashboard.xlsx** is maintained |
| Data       | Repository of all spreadsheets created                             |
| Format     | Repository of all the methods associated to the various formats    |
| Processing | Repository of all the methods associated to Processing             |
| ECB        | Scripts/ ECB specific info                                         |
| MAS        | Scripts/ MAS specific info                                         |
| FED        | Scripts/ FED specific info                                         |
| BoE        | Scripts/ BoE specific info                                         |
| …          |                                                                    |

# Control Dashboard

An Excel spreadsheet **Dashboard.xlsx** is updated with each run of
SpotImports. In addition to the various parameters used for the extract,
a unique batch ID is added and a link to the result xlsx file created.
That way we get:

1.  *A unique name for each and every extraction* If unicity constraints
    are required (FIS import) and/or the file is a repeat of a previous
    extract, in all cases the name being unique makes it easy to manage
2.  *The moment of extract* Knowing when the files were created but more
    importantly the parameters used for the extract is crucial to
    determine the expected contents of the generated spreadsheet
3.  *Easy access to the created spreadsheet* The generated excel
    spreadsheet - via the link - can be checked easily in order to
    verify the data

# Parameters

## Source

Indicates the source of the FX Rates

| Values    | Directory | Default | Comments                     |
|-----------|-----------|---------|------------------------------|
| ECB       | ECB       | D       | European Central Bank        |
| MAS       | MAS       |         | Monetary Authority Singapore |
| FED       | FED       |         | US Federal Reserve           |
| BoE       | BoE       |         | Bank of England              |
| RijksBank |           | Not Yet | Swedish Central Bank         |
|           |           |         |                              |

## Dates

All Dates are string in the following format yyyy-mm-dd

| Parameter  | Default | Comments                        |
|------------|---------|---------------------------------|
| -StartDate | D       | If not populated System Date    |
| -EndDate   | D       | If not populated StartDate -1   |
| -ListDate  |         | List of dates all in yyyy-mm-dd |
|            |         | , `comma` separated             |

### StartDate / EndDate

As a result of the default convention if no StartDate and no EndDate is
indicated then extraction happens only for Today and the day Before

### Dates

If Dates is populated then StartDate and EndDate are **NOT** taken into
account even if populated.

The Extract phase is done based on:

-   the minimum Date in the list = StartDate
-   the maximal Date in the list = EndDate

The result of the extraction is then filtered using the list of dates.

## BaseCurrency

Exchange Rates are always presented against a Base Currency. By default
the Base Currency is the Base Curency of the source.

| Source | Default Base Currency |
|--------|-----------------------|
| ECB    | EUR                   |
| MAS    | SGD                   |
| FED    | USD                   |

But in some cases, it might be required to present the Exchange Rates
against a different Base Currency from the one provided by the Source

An example could be:

-   Source = MAS // required Base Currency = USD

A triangulation needs to be added in order to transform the SGD/VND
provided into the required USD/VND. In a similar way the SGD/USD
provided is replaced by the USD/SGD

### Not yet required

BaseCurrency could be extended and become a list of BaseCurrency.

No extra gain in accuracy but a higher volume of data to be handled. As
that implies that the number of records generated will be n\*p where:

-   n is the number of currencies extract (cf. List Currencies or the
    list of currencies fron the Source Setup)
-   p is the number of currencies in the BaseCurrency parameter list.

## ListCCY

Important in order to be able to automatically indicate a subset of
currencies. By default the currencies processed are all trhe currencies
as provided by the Source For exanmple:

-   ListCurrencies from ECB will always integrate NOK,SEK but not THB or
    IDR
-   ListCurrencies from MAS will include THB,IDR but not …

## Output

| Values  | Default | Comments                                               |
|---------|---------|--------------------------------------------------------|
| CsvOnly |         | Only a CSV File based on the default separator         |
| CsvXlsx | D       |                                                        |
| XLS     |         | Generates an xls spreadsheet with the CSV              |
|         |         | Not implemented YET - Only if Import-Excel supports it |

## CSVSep

A list of possible values addesd ; \| whoch should cover the non-English
csv format Please note that TAB is currently not supported

| Values | Default | Comments             |
|--------|---------|----------------------|
| ,      | D       | Standard CSVSep      |
| ;      |         |                      |
| ¦      |         | Pipe could be useful |

## Processing

| Values   | Default | Comments                                   |
|----------|---------|--------------------------------------------|
| NoAction | D       | The generated file(s) are not processed    |
| F2       |         | Process through F2 mechanism               |
| FIS      | Future  | When using the automatic drop down for FIS |

## Format

| Values | Default | Comments   |
|--------|---------|------------|
| FIS    | D       | Investran  |
| F2     |         | F2 project |

## FISType

Option only is accepted is Format = FIS

| Values  | Default | Comments        |
|---------|---------|-----------------|
| FX Pair | D       |                 |
| FX Rate |         | To be validated |

## FISVariant

This is an option used for FIS only if Closing is not used. There is no
control about the value. Only a default value **Closing** is provided.

## Show

| Values | Default | Comments                                                |
|--------|---------|---------------------------------------------------------|
| Show   | D       | Display the contents of the FXRate spreadsheet in Excel |
| NoShow |         | Does not display anything in Excel.                     |

NoShow option is actually useful in different circumstances such as:

1.  Excel is NOT available
2.  For testing purposes when many extracts in succession are done In
    that last case, leveraging the Dahsboard.xlsx spreadsheet it is easy
    to examine the created spreadsheets

# Date Format

Theoretically precaution has beem taken to convert/handle Date in the
universal format yyyy-MM-dd That should work for both European and US
users although not tested for US users

## If necessary

Possibility to leverage Use-Culture to manage Date and potentially CSV
characteristics. Not tested and checked as it adds an extra dependency
Link below is a pointer to the alternative approach if required
<https://devblogs.microsoft.com/scripting/formatting-date-strings-with-powershell/>

# LemonEdge

## Test ECB

``` powershell
./SpotImports.ps1 -StartDate 2022-03-01 -EndDate 2022-03-04 -Source ECB -Format LE -ListCCY CHF,GBP,USD,EUR,CAD 
```

| IMPORT<sub>DATAACTION</sub> | IMPORT<sub>DATATYPE</sub> | ID  | EffectiveDate | FromCurrencyID<sub>ThreeLetterCode</sub> | ToCurrencyID<sub>ThreeLetterCode</sub> | Rate    |
|-----------------------------|---------------------------|-----|---------------|------------------------------------------|----------------------------------------|---------|
| Import                      | Exchange Rates            |     | 2022-03-04    | EUR                                      | USD                                    | 1.0929  |
| Import                      | Exchange Rates            |     | 2022-03-04    | EUR                                      | GBP                                    | 0.82388 |
| Import                      | Exchange Rates            |     | 2022-03-04    | EUR                                      | CHF                                    | 1.0056  |
| Import                      | Exchange Rates            |     | 2022-03-04    | EUR                                      | CAD                                    | 1.3937  |
| Import                      | Exchange Rates            |     | 2022-03-03    | EUR                                      | USD                                    | 1.1076  |
| Import                      | Exchange Rates            |     | 2022-03-03    | EUR                                      | GBP                                    | 0.82773 |
| Import                      | Exchange Rates            |     | 2022-03-03    | EUR                                      | CHF                                    | 1.0192  |
| Import                      | Exchange Rates            |     | 2022-03-03    | EUR                                      | CAD                                    | 1.3992  |
| Import                      | Exchange Rates            |     | 2022-03-02    | EUR                                      | USD                                    | 1.1106  |
| Import                      | Exchange Rates            |     | 2022-03-02    | EUR                                      | GBP                                    | 0.83316 |
| Import                      | Exchange Rates            |     | 2022-03-02    | EUR                                      | CHF                                    | 1.0216  |
| Import                      | Exchange Rates            |     | 2022-03-02    | EUR                                      | CAD                                    | 1.4088  |
| Import                      | Exchange Rates            |     | 2022-03-01    | EUR                                      | USD                                    | 1.1162  |
| Import                      | Exchange Rates            |     | 2022-03-01    | EUR                                      | GBP                                    | 0.8329  |
| Import                      | Exchange Rates            |     | 2022-03-01    | EUR                                      | CHF                                    | 1.0247  |
| Import                      | Exchange Rates            |     | 2022-03-01    | EUR                                      | CAD                                    | 1.4158  |

``` powershell
./SpotImports.ps1 -Dates 2022-03-04 -Source ECB -Format LE -ListCCY CHF,GBP,USD,EUR,CAD 
```

### Test 2022-03-10

Verification that the names range have been successfully added

``` powershell
./SpotImports.ps1 -StartDate 2022-03-04 -EndDate 2022-03-09 -Format LE -ListCCY CHF,GBP,USD,EUR,CAD -LECategory ECB
```

## Test BoE

LE is default output format … BoE does not work +/- as expected
2022-03-06

``` powershell
./SpotImports.ps1 -StartDate 2022-03-01 -EndDate 2022-03-04 -Source BoE -ListCCY CHF,GBP,USD,EUR,CAD 
```
