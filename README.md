# Azure NoSql Test (CosmosDB)

Python script to execute an Azure NoSql connectivity and Read/Write test

## Installation

Tested with Python 3.10 and Azure NoSql Pytho SDK. For libraries installations please use the following command:
```
pip install azure.cosmos

or 

pip3 install azure.cosmos
```

Make sure to have the Endpoint variable and the Primary Key in line 13, 14.

On line 11 you need to set as True or False in orde to choose if ReadWrite tests will be executed. 
