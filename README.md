[[_TOC_]]
# Objective

Provided script will test connectivity towards an endpoint of Azure CosmosDB NoSQL. There are currently two testing methods provided:
- Connectivity test
- Connectivity with Read/Write

For these tests, the authentication is performed with **Endpoint** and **Key** variable added in the corresponding location. 
There is a flag on the line 12 that needs to be configured with **True/False** in order to choose the execution of the ReadWrite test on the NoSQL resource. Also, the Database and Container name could be customized with desired values. As the libraries will return an error when the record already exists, there is implemented a word generator that will generate the Item id at every run. 

This script will provide also the execution time of the executed tests.
# Prerequisites

This script can be run on a Node or Pod level. It requires a Python3.x version along with the official Azure libraries for CosmosDB
This library can be installed with the following command:


```
pip install azure.cosmom
```

# Source code

Python source code:
```
import os
import json
import random
import array
from azure.cosmos import CosmosClient, PartitionKey
import azure.cosmos.exceptions as exceptions
import time


# Execute a Read Write Operation on a Database/Container/Item. Default False
ReadWriteTest = True

ENDPOINT = "https://endpoint.documents.azure.com"
KEY = "YourPrimaryKey"

DATABASE_NAME = "cosmicworks"
CONTAINER_NAME = "products"

def idGen():
  MAX_LEN = 12
  DIGITS = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9']
  LOCASE_CHARACTERS = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h',
                       'i', 'j', 'k', 'm', 'n', 'o', 'p', 'q',
                       'r', 's', 't', 'u', 'v', 'w', 'x', 'y',
                       'z']

  COMBINED_LIST = DIGITS + LOCASE_CHARACTERS
  rand_digit = random.choice(DIGITS)
  rand_lower = random.choice(LOCASE_CHARACTERS)
  temp_pass = rand_digit + rand_lower
  for x in range(MAX_LEN - 4):
      temp_pass = temp_pass + random.choice(COMBINED_LIST)
      temp_pass_list = array.array('u', temp_pass)
      random.shuffle(temp_pass_list)
  password = ""
  for x in temp_pass_list:
          password = password + x
  return password

def idGen():
  MAX_LEN = 12
  DIGITS = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9']
  LOCASE_CHARACTERS = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h',
                       'i', 'j', 'k', 'm', 'n', 'o', 'p', 'q',
                       'r', 's', 't', 'u', 'v', 'w', 'x', 'y',
                       'z']

  COMBINED_LIST = DIGITS + LOCASE_CHARACTERS
  rand_digit = random.choice(DIGITS)
  rand_lower = random.choice(LOCASE_CHARACTERS)
  temp_pass = rand_digit + rand_lower
  for x in range(MAX_LEN - 4):
      temp_pass = temp_pass + random.choice(COMBINED_LIST)
      temp_pass_list = array.array('u', temp_pass)
      random.shuffle(temp_pass_list)
  password = ""
  for x in temp_pass_list:
          password = password + x
  return password

def readWrite(container):
  container.create_item(new_item)
  existing_item = container.read_item(item=item_id, partition_key="61dba35b-4f02-45c5-b648-c6badc0cbd79",)
  print("--->>> Item name read\t", existing_item["name"])


def queryDB(container):
  QUERY = "SELECT * FROM products p WHERE p.categoryId = @categoryId"
  CATEGORYID = "61dba35b-4f02-45c5-b648-c6badc0cbd79"
  params = [dict(name="@categoryId", value=CATEGORYID)]
  results = container.query_items(query=QUERY, parameters=params, enable_cross_partition_query=False)
  items = [item for item in results]
  output = json.dumps(items, indent=True)
  print("Result list\t", output)

if __name__ == "__main__":

  item_id = idGen()
  category_id = "61dba35b-4f02-45c5-b648-c6badc0cbd79"
  new_item = {
      "id": item_id,
      "categoryId": category_id,
      "categoryName": "gadgets",
      "name": "Ovidiu Borlean",
      "quantity": 13,
      "sale": False,
    }



  try:
    startTime = time.time()
    print("Connecting to CosmodDB Endpoint: ", ENDPOINT)
    client = CosmosClient(url=ENDPOINT, credential=KEY)
    print("Successfully connected to: ", ENDPOINT)
    if ReadWriteTest == True:
       print("Executing Read/Write Tests")
       database = client.create_database_if_not_exists(id=DATABASE_NAME)
       print("Successfully created Database\n", database.id)
       key_path = PartitionKey(path="/categoryId")
       container = database.create_container_if_not_exists(id=CONTAINER_NAME, partition_key=key_path, offer_throughput=400)
       print("Successfully created Container\n", container.id)
       readWrite(container)
       #queryDB(container)
       print("Done Read/Write Operation")
    endTime = time.time()
    elapsed_time = endTime - startTime
    print('Execution time:', elapsed_time, 'seconds')
  except exceptions.CosmosHttpResponseError as e:
       print('\nrun_sample has caught an error. {0}'.format(e.message))
       print("Failed to connect to : ", ENDPOINT)
```
