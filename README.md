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

basic format of endpoints
```
@app.route('/')
def base():
    return Response(response=json.dumps({"Status": "UP"}),
                    status=200,
                    mimetype='application/json')
```
This would show Status Up message at (root) http://0.0.0.0:5001

Use http://0.0.0.0:5001/mongodb
For CRUD operations (GET,POST,PUT,DELETE)

import libraries pymongo,flask
### CRUD operations

Class MongoAPI is defined to interact with mongodb  <br />
Connection to cluster is defined inside __init__ function <br />
Client is predefined and database and cluster names are to be passed.
read, write, update and delete functions are defined under the class
And data is passed from app endpoints via various methods

Database used is loaded in
database : Example
collection : test1

After running the project using docker run
Go to http://0.0.0.0:5001/mongodb (page for queries) <br />
Sending database and collection info is necessary for any operation

READ <br />
Send database and collection info as json. It returns the whole data
```
@app.route('/mongodb', methods=['GET'])
def mongo_read():
    data = request.json
    if data is None or data == {}:
        return Response(response=json.dumps({"Error": "Please provide connection information"}),
                        status=400,
                        mimetype='application/json')
    obj1 = MongoAPI(data)
    response = obj1.read()
    return Response(response=json.dumps(response),
                    status=200,
                    mimetype='application/json')
```
data is passed to the above function and it calls read function within the class <br />
An error 400 is specified to catch the exception when no data is passed
```
def read(self):
       log.info('Reading All Data')
       documents = self.collection.find()
       output = [{item: data[item] for item in data if item != '_id'} for data in documents]
       return output
```
find() is used to retrieve the documents

Write <br />
Send the default info (connection information) plus the data you want to create
```
@app.route('/mongodb', methods=['POST'])
def mongo_write():
    data = request.json
    if data is None or data == {} or 'Document' not in data:
        return Response(response=json.dumps({"Error": "Please provide connection information"}),
                        status=400,
                        mimetype='application/json')
    obj1 = MongoAPI(data)
    response = obj1.write(data)
    return Response(response=json.dumps(response),
                    status=200,
                    mimetype='application/json')
```

```
def write(self, data):
       log.info('Writing Data')
       new_document = data['Document']
       response = self.collection.insert_one(new_document)
       output = {'Status': 'Successfully Inserted',
                 'Document_ID': str(response.inserted_id)}
       return output

```
Update <br />
Send the default info plus filter (any attribute to identify the document you want to change) and datatobeupdated (new value)
```
@app.route('/mongodb', methods=['PUT'])
def mongo_update():
    data = request.json
    if data is None or data == {} or 'Filter' not in data:
        return Response(response=json.dumps({"Error": "Please provide connection information"}),
                        status=400,
                        mimetype='application/json')
    obj1 = MongoAPI(data)
    response = obj1.update()
    return Response(response=json.dumps(response),
                    status=200,
                    mimetype='application/json')
```

```
def update(self):
      log.info('Updating Data')
      filt = self.data['Filter']
      updated_data = {"$set": self.data['DataToBeUpdated']}
      response = self.collection.update_one(filt, updated_data)
      output = {'Status': 'Product detail Successfully Updated' if response.modified_count > 0 else "Nothing was updated."}
      return output

```
Delete <br />
Send the default info plus filter (any attribute to identify the document you want to Delete)
```
@app.route('/mongodb', methods=['DELETE'])
def mongo_delete():
    data = request.json
    if data is None or data == {} or 'Filter' not in data:
        return Response(response=json.dumps({"Error": "Please provide connection information"}),
                        status=400,
                        mimetype='application/json')
    obj1 = MongoAPI(data)
    response = obj1.delete(data)
    return Response(response=json.dumps(response),
                    status=200,
                    mimetype='application/json')
```
```
def delete(self, data):
        log.info('Deleting Data')
        filt = data['Filter']
        response = self.collection.delete_one(filt)
        output = {'Status': 'Product successfully Deleted' if response.deleted_count > 0 else "Product not found."}
        return output

```

That's it for the CRUD operations

### Approach

Being the first ever Flask, Mongodb, Docker Project I ever touched and being the first time hearing concept of REST api, <br />
First step was learning these concepts starting from REST api <br />
Database was set up in Mongodb Atlas <br />
MongoDb Compass was used to view the database and changes

Some troublesome codes were

Missing out on importing Response from Flask <br />
Trying to figure out sending of json without having a TypeView error <br />
Before dockerising flask app was to run with host and port commands  <br />
in cmd else default localhost/5000 kicks in <br />
dnspython was a library I missed out on <br />
It had to be added to requirement so necessary change was added to pymongo <br />
to include extras <br />
Another issue was docker getting in trouble with pip and pip3 <br />
An extensive search for the same gave a workaround using IF condition to  <br />
explicitly specify the correct usage and not get into trouble <br />
Common errors to be faced when starting crud operations would be
Connection Refused (Check host and port default is 0.0.0.0 and 5001) <br />
TypeView error (variety of reasons most common being treatment of request response) <br />
Method not allowed could have popped up when incorrect operation is used  <br />
(for example to specify get method only in app code and tried post operation)
