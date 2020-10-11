# MongoDocked
 Crud operations on Flask and Mongo Dockerized
## Getting Started
### Installation
install docker
(Mongodb atlas was setup and can also work with any database provided you edit the info in restapi )
To deploy the docker container
Run the docker command
```
docker-compose build
docker-compose up
```
This would run the project at host 0.0.0.0 and port 5001

### Accessing Api on localhost

go to http://0.0.0.0:5001
(The root) Successful initiation see the message 'Status UP'

Use http://0.0.0.0:5001/mongodb
For CRUD operations

### CRUD operations
Here we are using a Product info CSV file of 5000 product data
Start up Postman
After running the project
Go to http://0.0.0.0:5001/mongodb
Sending database and collection info is necessary for any operation
READ
Send database and collection info as json it returns the whole data
```
{
  "database": "Example",
  "collection": "test1"
}
```
Write
Send the default info plus the data you want to create
```
{
  "database": "Example",
  "collection": "test1",
  "Document": {
        "brand_name": "robocop",
        "classification_l1": "baby & child",
        "classification_l2": "soft toys",
        "classification_l3": "",
        "classification_l4": "",
        "currency": "GBP",
        "image_url": "",
        "name": "Robocop advanced ",
        "offer_price_value": 10,
        "regular_price_value": 12
  }
}
```
Update
Send the default info plus filter (any attribute to identify the document you want to change) and datatobeupdated (new value)
```
{
  "database": "Example",
  "collection": "test1",
  "Filter": {
     "brand_name": "robocop"
  },
  "DataToBeUpdated": {
   "classification_l3": "dvd"
  }
}

```
Delete
Send the default info plus filter (any attribute to identify the document you want to Delete)
```
{
  "database": "Example",
  "collection": "test1",
  "Filter": {
     "brand_name": "robocop"
  }
}
```

...Thats it for the CRUD operations

###Problems i ran into early on

Being the first ever Flask,Mongodb,Docker Project i ever touched and being the first time hearing concept of REST api,
first step was learning these concepts starting from REST api
@ishmeet1995 was the main source of learning and can see a major influence in the code too.

Now initial thoughts was setting up mongodb without atlas and half way down i felt atlas was a lot easier for the same task.
I used MongoDb Compass to view the database
Some troublesome codes where
Missing out on importing Response from Flask
Trying to figure out sending of json without having a Typeviewerror
Before dockerising flask app was to run with host and port commands in cmd else default localhost/5000 kicks in
dnspython was a library i missed out on.
It had to be added to requirement so necessary change was added to pymongo to include extras
Another issue was docker getting in trouble with pip and pip3
An extensive search for the same gave a workaround using IF condition to explicitely specify the correct usage and not get into trouble
Common errors to be faced when starting crud operations would be
Connection Refused (Check host and port default is 0.0.0.0 and 5001)
TypeView error (variety of reasons most common being treatment of request response)
Method not allowed could have popped up when incorrect operation is used (maybe u specify get method only and tried post)
