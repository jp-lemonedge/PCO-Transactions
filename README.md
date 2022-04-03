# Objectives

The necessary components to deploy a new functionality are grouped in a
package where all the various components can be found. For the time
being this package is made of:

1.  SQL Wrappers
2.  Static Data / Config Upload
3.  Examples/test to verify the functionality

By deploying the various components in the proposed order, it should be
easy to create on a different environment the exact functionalities. In
the case of that component, the deployment has been tested:

-   locally on an unrelated database
-   for a customer on its environment - in that case Colmore

# Package objectives

## Standard Objective

The objective is to be able to enter easily transactions in the system
to track a direct investment into a Portfiolio company and generate
automatically the GL Postings.

The following table summarizes the amount to be entered and the
calculation process made on the back of it

| Transaction Code | Nb Amounts | Definition of Amounts               | GL Entries                                 |
|------------------|------------|-------------------------------------|--------------------------------------------|
| PCO Buy          | 1          | Amount of Investment                | Amount vs. Cash                            |
| PCO Valuation    | 1          | **TOTAL** amount of the valuation   | Increase/or decrease of the unrealised P&L |
|                  |            | The system calculates automatically |                                            |
|                  |            | the variation of valuation to be    |                                            |
|                  |            | reflected in the accounting system  |                                            |

## Colmore Objective

# Deployment

Each party should be deployed used in the order described

On a database where accounting is +/- similar

## Step 1 - SQL Wrapper

![Standard Import
mechanism](./images/2022-04-03T135304.png "./images/2022-04-03T135304.png")

Just select the xml file which have been previously exported. Note that
by default choosing Import/Server/Sequential the files presentes are
xlsx files. In that case, it is necessary to select XML. As a result 4
SQL Wrappers will be created.

PS: As per 2022-04-03 there is a bug regarding the indexing/order of the
parameter. Some verification should be made to verify that the
parameters (the first 4) are in the expected order
Account/Canvas/Team/As Of. That should be the case but as long as the
underlying bug is not sorted out some issues may appear.

As a result the 4 SQL Wrappers will be deployed. Their description is
all prefixed by COLT (for COLmore Transactions) and thus could be found
easily.

![The 4 SQL Wrapper of the
package](./images/2022-04-03T180659.png "./images/2022-04-03T180659.png")

## Step 2 - Static Data

Next step is to upload the xlsx file **PCO-Transactions Static
Data.xlsx**. It will take probably 3 mns+ for the upload to be
successful. If all has been successful the following Information message
should be displayed.

![Information
Message](./images/2022-04-03T142106.png "./images/2022-04-03T142106.png")

Among the different data which have been imported, the following
Transaction Codes have been created.

![PCO Transactions have been
uploaded](./images/2022-04-03T141714.png "./images/2022-04-03T141714.png")

They have been associated to certain Transaction Types as per the
snapshot below for PCO Sale

![Definition of Transaction Types
etc..](./images/2022-04-03T141756.png "./images/2022-04-03T141756.png")

### Warning - Assumptions.

The import makes very little assumption about what exists in the
database. Nevertheless it assumes that:

1.  A ledger named Base exists in the system If the ledger does not
    exist, some error message will appear. Please add that ledger

2.  That certain GL Accounts exist. The list of expected GL Accounts is

| List of Accounts             |
|------------------------------|
| Cash                         |
| Clearing Fees                |
| Other Income/Expense         |
| 16000 - Investment Cost      |
| 56020 - Realized P/L         |
| 56010 - Unrealized Gain/Loss |

If these accounts do not exist, it is only the generation of the GL
Entry which will fail. The rest will work as expected. Any database
derived from LT<sub>CapitalDemo</sub> should have all these accounts
already defined

## Step 3 - Test Cases

In the directory *Test Cases*, there are 2 Excel spreadsheets ready to
import a set of new Transactions for different Instruments. The 2
spreadsheets serve 2 different purposes (cf. objective)

1.  <u>Test PCO Transactions</u> This spreadsheet will create a set of
    Transactions of type PCO Buy/PCO Sale/PCO Valuation. That is thus an
    example of tracking directly an investment
2.  <u>Feb-Marc-Apr 2022 PCO Transactions</u> Is an example on 3 months
    using only Transactions of type PCO Transaction. It is an example
    primarily focused for Colmore to track the investments in PCO from
    an Investee Funds. In that case the PCO Transactions reflect exactly
    what is provided by the Investee Fund i.e. 5 balances:
    -   The Investment at Costs
    -   The Valuation of the Investments
    -   The @Cost Amount which has been sold
    -   The total proceeds which is a sum of Sales Proceeds + Other
        Incomes/Expenses
    -   The sum of all other Imcome Expenses

Note that these spreadsheets are NOT ready to use as they refer to a lot
which probably does not exist in the target environment. When deploying
the spreadsheets, please modify the lot to reflect existing lots in your
system. In order to test the deployment please use a "fresh" lot i.e. a
lot where no previous transactions has been booked.
