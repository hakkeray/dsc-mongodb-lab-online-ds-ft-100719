
# MongoDB - Lab

## Introduction

In this lesson, we'll get some hands-on experience with MongoDB!

## Objectives
You will be able to: 

- Create a MongoDB database   
- Insert data into a MongoDB database   
- Read data from a MongoDB database   
- Update data in a MongoDB database   

## Getting Started

To begin this lab, make sure that you start up the mongoDB server in your terminal first! **You must do this lab locally on your computer, not in Learn.**


## Connecting to the MongoDB Database

In the cell below, import the appropriate library and connect to the mongoDB server. Create a new database called `'lab_db'`.


```python
!pip install pymongo
```

    Collecting pymongo
    [?25l  Downloading https://files.pythonhosted.org/packages/fc/79/c298dfe767f6937f0d5eaefa412ca6897ec27c1834e325a71476c7c6f2ce/pymongo-3.9.0-cp36-cp36m-macosx_10_6_intel.whl (378kB)
    [K     |████████████████████████████████| 378kB 10.1MB/s eta 0:00:01
    [?25hInstalling collected packages: pymongo
    Successfully installed pymongo-3.9.0



```python
import pymongo
```


```python
myclient = pymongo.MongoClient("mongodb://127.0.0.1:27017/")
labdb = myclient['lab_db']
```

## Creating a Collection

Now, create a collection called `'lab_collection'` inside `'lab_db'`.


```python
lab_collection = labdb['lab_db']
```

## Adding Some Data

Now, we're going to add some example records into our database. In the cells below, create dictionary representations of the following customer records:


|     Name     |            Email           |  Mailing_Address  | Balance |                         Notes                         |
|:------------:|:--------------------------:|:-----------------:|:-------:|:-----------------------------------------------------:|
|  John Smith  |    j.smith@thesmiths.com   | 123 mulberry lane |   0.0   |    Called technical support, issue not yet resolved   |
|  Jane Smith  |  jane_smith@thesmiths.com  |         Null          |  25.00  |                   Null                                    |
|  Adam Enbar  | adam@theflatironschool.com |    11 Broadway    |  14.99  |           Set up on recurring billing cycle           |
| Avi Flombaum |  avi@theflatironschool.com |    11 Broadway    |   0.0   |                   Null                                    |
|   Steven S.  |     steven.s@gmail.net     |         Null          |  -20.23 | Refunded for overpayment due to price match guarantee |


Your first challenge is to take all of the data in the table above and create the corresponding documents and add then to our mongo database. Note that fields that contain 'Null' should not be included in the document (recall that since mongo is schema-less, each document can be different). 

Create the documents from the table listed above, and then use `insert_many()` to insert them into the collection.


```python
customer_1 = {'name': 'John Smith', 'email': 'j.smith@thesmiths.com', 'mailing_address': '123 mulberry lane', 'balance': 0.0, 'notes':'Called technical support, issue not yet resolved'}
customer_2 = {'name': 'Jane Smith', 'email': 'jane_smith@thesmiths.com', 'balance': 25.00}
customer_4 = {'name': 'Avi Flombaum', 'email': 'avi@theflatironschool.com', 'mailing_address': '11 Broadway', 'balance': 0.0}
customer_5 = {'name': 'Steven S.', 'email': 'steven.s@gmail.net', 'balance': -20.23, 'notes':'Refunded for overpayment due to price match guarantee'}
customer_3 = {'name': 'Adam Enbar', 'email': 'adam@theflatironschool.com', 'mailing_address': '11 Broadway', 'balance':14.99, 'notes': 'setup on recurring billing cycle'}
```


```python
customers = [customer_1, customer_2, customer_3, customer_4, customer_5]
```


```python
all_records = lab_collection.insert_many(customers)
```


```python
insertion_results = all_records
```

Now, access the appropriate attribute from the results object returned from the insertion to see the unique IDs for each record inserted, so that we can confirm each were inserted correctly. 


```python
insertion_results.inserted_ids
```




    [ObjectId('5dcddd70662ac8637114e48b'),
     ObjectId('5dcddd70662ac8637114e48c'),
     ObjectId('5dcddd70662ac8637114e48d'),
     ObjectId('5dcddd70662ac8637114e48e'),
     ObjectId('5dcddd70662ac8637114e48f')]



## Querying and Filtering

In the cell below, return the name and email address for every customer record. Then, print every item from the query to show that it worked correctly. 


```python
query_1 = lab_collection.find({},{'name': 1, 'email':1})
for item in query_1:
    print(item)
```

    {'_id': ObjectId('5dcddd70662ac8637114e48b'), 'name': 'John Smith', 'email': 'j.smith@thesmiths.com'}
    {'_id': ObjectId('5dcddd70662ac8637114e48c'), 'name': 'Jane Smith', 'email': 'jane_smith@thesmiths.com'}
    {'_id': ObjectId('5dcddd70662ac8637114e48d'), 'name': 'Adam Enbar', 'email': 'adam@theflatironschool.com'}
    {'_id': ObjectId('5dcddd70662ac8637114e48e'), 'name': 'Avi Flombaum', 'email': 'avi@theflatironschool.com'}
    {'_id': ObjectId('5dcddd70662ac8637114e48f'), 'name': 'Steven S.', 'email': 'steven.s@gmail.net'}


Great! Now, let's write a query that gets an individual record based on a stored key-value pair a document contains. 

In the cell below, write a query to get the record for `'John Smith'` by using his name. Then, print the results of the query to demonstrate that it worked correctly.  


```python
query_2 = lab_collection.find({'name': 'John Smith'})
for item in query_2:
    print(item)
```

    {'_id': ObjectId('5dcddd70662ac8637114e48b'), 'name': 'John Smith', 'email': 'j.smith@thesmiths.com', 'mailing_address': '123 mulberry lane', 'balance': 0.0, 'notes': 'Called technical support, issue not yet resolved'}


Great! Now, write a query to get the names, email addresses, and balances for customers that have a balance greater than 0. Use a modifier to do this. 

**_HINT_**: In the query below, you'll be passing in two separate dictionaries. The first one defines the logic of the query, while the second tells which fields we want returned. 


```python
query_3 = lab_collection.find( {'balance': {'$gt': 0}})
for item in query_3:
    print(item)
```

    {'_id': ObjectId('5dcddd70662ac8637114e48c'), 'name': 'Jane Smith', 'email': 'jane_smith@thesmiths.com', 'balance': 25.0}
    {'_id': ObjectId('5dcddd70662ac8637114e48d'), 'name': 'Adam Enbar', 'email': 'adam@theflatironschool.com', 'mailing_address': '11 Broadway', 'balance': 14.99, 'notes': 'setup on recurring billing cycle'}


## Updating a Record

Now, let's update some records. In the cell below. set the mailing address for `'John Smith'` to `'367 55th St., apt 2A'`.


```python
record_to_update_1 = {'name': 'John Smith'}
update_1 = {'$set': {'mailing_address': '367 55th St., apt 2A'}}

lab_collection.update_one(record_to_update_1, update_1)
```




    <pymongo.results.UpdateResult at 0x11c1dad08>



Now, write a query to check that the update worked for this document in the cell below:  


```python
query_4 = lab_collection.find({'name': 'John Smith'})
for item in query_4:
    print(item)
```

    {'_id': ObjectId('5dcddd70662ac8637114e48b'), 'name': 'John Smith', 'email': 'j.smith@thesmiths.com', 'mailing_address': '367 55th St., apt 2A', 'balance': 0.0, 'notes': 'Called technical support, issue not yet resolved'}


Now, let's assume that we want to add birthdays for each customer record. Consider the following table:

|     Name     |  Birthday  |
|:------------:|:----------:|
|  John Smith  | 02/20/1986 |
|  Jane Smith  | 07/07/1983 |
|  Adam Enbar  | 12/02/1982 |
| Avi Flombaum | 04/17/1983 |
|   Steven S.  | 08/30/1991 |

We want to add birthdays for each person, but we want to do so in a way where we don't have to do the same repetitive task over and over again. This seems like a good opportunity to write a function to handle some of the logic for us!

In the cell below:

* Store the names in the `names_list` variable as a list.
* Store the birthdays in the `birthdays_list` variable as a list.
* Write a function that takes in the two different lists, and updates each record by adding in the appropriate key-value pair containing that user's birthday.

**_Hint:_** There are several ways that you could write this, depending on whether you want to use the `update_one()` method or the `update_many()` method. See if you can figure out both approaches!


```python
def update_birthdays(names, birthdays):
    name_bday = dict(zip(names, birthdays))
    for k, v in name_bday.items():
        rec = {'name': k}
        bday = {'$set': {'birthday': v}}
        lab_collection.update_one(rec, bday)
```


```python
names_list = ['John Smith', 'Jane Smith', 'Adam Enbar', 'Avi Flombaum', 'Steven S.']
birthdays_list = ['02/20/1986', '07/07/1983', '12/02/1982', '04/17/1983', '08/30/1991']
        
update_birthdays(names_list, birthdays_list)
```

Now, write a query to check your work and see that the birthdays were added correctly.


```python
query_5 = lab_collection.find({})
for item in query_5:
    print(item)
```

    {'_id': ObjectId('5dcddd70662ac8637114e48b'), 'name': 'John Smith', 'email': 'j.smith@thesmiths.com', 'mailing_address': '367 55th St., apt 2A', 'balance': 0.0, 'notes': 'Called technical support, issue not yet resolved', 'birthday': '02/20/1986'}
    {'_id': ObjectId('5dcddd70662ac8637114e48c'), 'name': 'Jane Smith', 'email': 'jane_smith@thesmiths.com', 'balance': 25.0, 'birthday': '07/07/1983'}
    {'_id': ObjectId('5dcddd70662ac8637114e48d'), 'name': 'Adam Enbar', 'email': 'adam@theflatironschool.com', 'mailing_address': '11 Broadway', 'balance': 14.99, 'notes': 'setup on recurring billing cycle', 'birthday': '12/02/1982'}
    {'_id': ObjectId('5dcddd70662ac8637114e48e'), 'name': 'Avi Flombaum', 'email': 'avi@theflatironschool.com', 'mailing_address': '11 Broadway', 'balance': 0.0, 'birthday': '04/17/1983'}
    {'_id': ObjectId('5dcddd70662ac8637114e48f'), 'name': 'Steven S.', 'email': 'steven.s@gmail.net', 'balance': -20.23, 'notes': 'Refunded for overpayment due to price match guarantee', 'birthday': '08/30/1991'}


Great! It looks like the birthdays have been successfully added to every record correctly!

## Summary

In this lesson, we got some hands-on practice working with MongoDB!
