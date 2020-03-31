# Mock Order API
A simple order api with CRUD operations

### Prerequisites

  - Apache Maven 3+
  - [Martini Desktop](https://www.torocloud.com/martini/download)

### Building the Martini Package

```
$ mvn clean package
```
This will create a ZIP file named `mock-order-api-bin.zip` containing all the files (services, configurations, etc.) needed under the `target` folder. This ZIP file is what we call a [Martini Package](https://docs.torocloud.com/martini/latest/developing/package/) which then you can import in Martini Desktop to get started. You can learn more how to import a Martini Package by visiting our [documentation](https://docs.torocloud.com/martini/latest/developing/package/importing/)

### Usage
This package exposes operations for a simple inventory REST API that allows you to do CRUD operations. You can find the [Gloop REST API](https://docs.torocloud.com/martini/latest/developing/gloop/api/rest/) file at `io/toro/mock/order/api/Order` under the `code` folder after importing the package to your Martini Desktop application.

### Setup
This Martini Package automatically sets up the required resources for you. During the package startup, Martini will execute the configured _Startup Service_ that initializes the database to be used for storing the record that will be creating for using this mock api.

This package uses HSQL as the default datastore but you can change this to MySQL by updating the [package properties](https://docs.torocloud.com/martini/latest/developing/package/properties/) located at [conf](https://docs.torocloud.com/martini/latest/developing/package/directory/) folder. Below is what the contents of the properties file look like

```
# Flag used for testing
debug.enabled=true

# The database type to be used
database.type=hsql

# HSQL database configuration to be used for testing environment
hsql.driver=org.hsqldb.jdbc.JDBCDriver
hsql.connection.url=jdbc:hsqldb:file:${toroesb.home}data/hsql/mock_order_api.db;hsqldb.tx=MVCC;sql.syntax_mys=true
hsql.username=sa
hsql.password=

# MySQL database configuration to be used in production environment
mysql.driver=com.mysql.cj.jdbc.Driver
mysql.connection.url=jdbc:mysql://<host>/<database-to-use>
mysql.username=root
mysql.password=
```
* `debug.enabled` when set to `true` initializes the database with dummy data when the package starts. The initialized data are also removed when the package stops or is unloaded. Set the value of this property to `false` if you want to keep your data when doing a restart. You can also set this property to `true` when the package starts and then set to `false` afterwards so that you'll have the data initialized on startup, and keep the data when the package or the Martini instance do a restart
* `database.type` sets the database provider the Martini package will use. If you will use the default hsql config, you don't need to change anything in the hsql configuration. **Note**: If you will use a different hsql database, make sure that you add `sql.syntax_mys=true` in the connection properties. This ensures that the SQL query from the SQL Services in this package will be compatible with hsql.
 
### Custom Fields

The order API's `/orders` endpoint currently supports custom fields but note that you can only add and edit custom fields as of this time.

#### Updating the Custom Fields

When updating an existing custom field, make sure you are including the `customField` property on the JSON body you are sending in the request like so:

```
{
    ...
    "customField": {
        "testField": "updated value"
    }
}
```

if the property exists already, it will update its existing value, if not, a new property will be created.

### Operations

The base url is `<host>/api/mock-order-api` where `host` is the location where the Martini is deployed. By default, it's `localhost:8080`.

#### Items

Not to confuse with the Mock Inventory API, as each mock apis are written to be standalone, so this mock api has its own exposed endpoint for adding inventory/product items.

`GET /items`

Returns a list of inventory items

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-order-api/items \
  -H 'accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200` with the response payload below:
```
[
    {
        "itemId": "403b4d54-847d-45eb-9ead-ad9c52b23d28",
        "dateCreated": 1583208938000,
        "dateUpdated": 1583208938000,
        "name": "Speakers",
        "description": "odio in",
        "price": 41.24
    },
    ...
    {
        "itemId": "ed47523f-4b09-40e6-92e0-81e3b03df939",
        "dateCreated": 1583208938000,
        "dateUpdated": 1583208938000,
        "name": "Mac-mini",
        "description": "dictumst maecenas",
        "price": 47.25
    }
]
```

`POST /items`

Create a new inventory item

**Sample Request**

**curl**
```
curl -X POST \
  http://localhost:8080/api/mock-order-api/items \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Shampoo",
    "description": "smp-1",
    "price": 19.99
}'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `201` with the response payload similar below.
```
{
    "result": "SUCCESS",
    "message": "Successfully added new item with id: 6c6460b2-99df-4e51-8239-f31e039cff3d."
}
```

`GET /items/<itemId>`

Returns a single inventory item that matches the given `itemId`

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-order-api/items/6c6460b2-99df-4e51-8239-f31e039cff3d \
  -H 'Accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200` with the response payload below.
```
{
    "itemId": "6c6460b2-99df-4e51-8239-f31e039cff3d",
    "dateCreated": 1583219591000,
    "dateUpdated": 1583219591000,
    "name": "Shampoo",
    "description": "Industrial Shampoo",
    "price": 19.99
}
```

`PATCH /items/<itemId>`

Updates the inventory item that matches the given `itemId`

**curl**
```
curl -X PATCH \
  http://localhost:8080/api/mock-order-api/items/6c6460b2-99df-4e51-8239-f31e039cff3d \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
    "description": "Updated description",
    "price": 15.99
}'
```

**Sample Response** 

If the request is successful, it will return an HTTP status code of `200` with the response payload below.
```
{
    "result": "SUCCESS",
    "message": "Successfully updated item with id: 6c6460b2-99df-4e51-8239-f31e039cff3d."
}
```

`DELETE /items/<itemId>`

Deletes a inventory item that matches the `itemId`

**Sample Request**

**curl**
```
curl -X DELETE \
  http://localhost:8080/api/mock-order-api/items/6c6460b2-99df-4e51-8239-f31e039cff3d
```

**Sample Response**

If the request is successful, it will return an HTTP status code of `204`.

#### Customer

`GET /customers`

Returns a list of customers

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-order-api/customers \
  -H 'accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code of 200 with the response payload below:
```
[
    {
        "customerId": "2a029a12-f010-416f-ada2-f033a49a81c5",
        "dateCreated": 1583283552000,
        "dateUpdated": 1583283552000,
        "name": "Hiram Luck",
        "address": "Egypt",
        "email": "hluck2@microsoft.com",
        "phoneNumber": "118-412-0977"
    },
    ...
    {
        "customerId": "f7657cde-b782-4af6-ab21-b4002b063049",
        "dateCreated": 1583283552000,
        "dateUpdated": 1583283552000,
        "name": "Ora Libbey",
        "address": "Philippines",
        "email": "olibbey1@jigsy.com",
        "phoneNumber": "756-706-4710"
    }
]
```

`POST /customers`

Creates a new customer

**Sample Request**

**curl**
```
curl -X POST \
  http://localhost:8080/api/mock-order-api/customers \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "John Doe",
    "address": "123 Street",
    "email": "jdoe@mail.com",
    "phoneNumber": "(789) 207-7366"
}'
```

**Sample Response**

If the request is successful, it will return an HTTP status code of `201` with the response payload below:
```
{
    "result": "SUCCESS",
    "message": "Successfully added customer with id \"078ee2da-c459-43d2-9a55-3a4ea9e18e91\"."
}
```

`GET /customers/<customerId>`

Returns a single customer data that matches the given `customerId`

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-order-api/customers/078ee2da-c459-43d2-9a55-3a4ea9e18e91 \
  -H 'Accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200` with the response payload below:
```
{
    "customerId": "078ee2da-c459-43d2-9a55-3a4ea9e18e91",
    "dateCreated": 1583285872000,
    "dateUpdated": 1583285872000,
    "name": "John Doe",
    "address": "123 Street",
    "email": "jdoe@mail.com",
    "phoneNumber": "(789) 207-7366"
}
```

`PATCH /customers/<customerId>`

Updates a customer record that matches the given `customerId`

**Sample Request**

**curl**
```
curl -X PATCH \
  http://localhost:8080/api/mock-order-api/customers \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
    "address": "123 Street",
    "phoneNumber": "(564) 593-8988"
}'
```

**Sample Response**

If the request is successful, it will return an HTTP status code of `200` with the response payload below:
```
{
    "result": "SUCCESS",
    "message": "Successfully updated details of customer with id \"078ee2da-c459-43d2-9a55-3a4ea9e18e91\"."
}
```

`DELETE /customers/<customerId>`

Deletes a customer record that matches the given `customerId`

**Sample Request**

**curl**
```
curl -X DELETE \
  http://localhost:8080/api/mock-order-api/customers/078ee2da-c459-43d2-9a55-3a4ea9e18e91
```

**Sample Response**

If the request is successful, it will return an HTTP status code `204`

#### Orders

`GET /orders`

Returns a list of all orders

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-order-api/orders \
  -H 'accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200` with the response payload below:
```
[
    {
        "orderId": "4de536e2-9e90-438b-9c5d-ec94fcf6a317",
        "dateCreated": 1583361695000,
        "dateUpdated": 1583361695000,
        "status": "NEW",
        "totalPrice": 94.5,
        "customer": {
            "customerId": "f370e377-a166-493c-9447-ed5ba60d63f0",
            "dateCreated": 1583361694000,
            "dateUpdated": 1583361694000,
            "name": "Laurent Dysart",
            "address": "Philippines",
            "email": "ldysart4@free.fr",
            "phoneNumber": "111-282-2284"
        },
        "orderItems": [
            {
                "id": "ed47523f-4b09-40e6-92e0-81e3b03df939",
                "name": "Mac-mini",
                "description": "dictumst maecenas",
                "price": 94.5,
                "quantity": 2
            }
        ],
        "customFields": {

        }
    },
    ...
    {
        "orderId": "e209db02-a07a-458a-8d10-c8240718bbfd",
        "dateCreated": 1583361695000,
        "dateUpdated": 1583361695000,
        "status": "NEW",
        "totalPrice": 349.68,
        "customer": {
            "customerId": "c5cd9579-bad8-492c-b541-0f14bda2faec",
            "dateCreated": 1583361694000,
            "dateUpdated": 1583361694000,
            "name": "Marysa Lytlle",
            "address": "Sweden",
            "email": "mlytlle6@slideshare.net",
            "phoneNumber": "941-581-5897"
        },
        "orderItems": [
            {
                "id": "74d07572-7c01-4457-8e08-c91b8e14ebc6",
                "name": "iMac",
                "description": "morbi odio odio elementum eu",
                "price": 349.68,
                "quantity": 6
            }
        ],
        "customFields": {

        }
    }
]
```

`POST /orders`

Creates a new order record

**Sample Request with custom field**

**curl**
```
curl -X POST \
  http://localhost:8080/api/mock-order-api/orders \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
    "status": "NEW",
    "customer": {
        "customerId": "2a029a12-f010-416f-ada2-f033a49a81c"
    },
    "orderItem": [
        {
            "itemId": "403b4d54-847d-45eb-9ead-ad9c52b23d28",
            "quantity": "2"
        }
    ],
    "customField": {
		"testField": "Test value"
    }
}'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `201` with the response payload below:
```
{
    "result": "SUCCESS",
    "message": "Successfully created Order with id \"56f9e2a5-7649-45ec-a049-bdba99c5ad3a\"."
}
```

`GET /orders/<orderId>`

Returns a single order record that matches the given `orderId`

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-order-api/orders/56f9e2a5-7649-45ec-a049-bdba99c5ad3a \
  -H 'Accept: application/json'
```

**Sample Response**

If the request is sucessful, it will return an HTTP response `200` with the response payload below:
```
{
    "orderId": "56f9e2a5-7649-45ec-a049-bdba99c5ad3a",
    "dateCreated": 1583429351000,
    "dateUpdated": 1583429351000,
    "status": "NEW",
    "totalPrice": 82.48,
    "customer": {
        "customerId": "2a029a12-f010-416f-ada2-f033a49a81c5",
        "dateCreated": 1583427845000,
        "dateUpdated": 1583427845000,
        "name": "Hiram Luck",
        "address": "Egypt",
        "email": "hluck2@microsoft.com",
        "phoneNumber": "118-412-0977"
    },
    "orderItems": [
        {
            "id": "403b4d54-847d-45eb-9ead-ad9c52b23d28",
            "name": "Speakers",
            "description": "odio in",
            "price": 82.48,
            "quantity": 2
        }
    ],
    "customFields": {
        "testField": "Test value"
    }
}
```

`PATCH /orders/<orderId>`

Updates details about an order. Currently it only supports changing the customer record associated to the order, adding custom fields, and updating the order `status`. Available properties for `status` are: `NEW`, `PROCESSING`, `PENDING`, `BACK_ORDER`, `COMPLETED`, `SHIPPED`.

**Sample Request**

**curl**
```
curl -X PATCH \
  http://localhost:8080/api/mock-order-api/orders/56f9e2a5-7649-45ec-a049-bdba99c5ad3a \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
    "status": "PENDING",
    "customer": {
        "customerId": "3857a037-d4d7-415a-a16f-7081a916e738"
    },
    "customField": {
		"testField": "Updated test value"
    }
}'
```

**Sample Response**

If the request is successful, it will return an HTTP status `200` with the response payload below:
```
{
    "result": "SUCCESS",
    "message": "Successfully updated of order with id \"56f9e2a5-7649-45ec-a049-bdba99c5ad3a\"."
}
```

`DELETE /orders/<orderId>`

Deletes an order record that matches the given `orderId`

**Sample Request**

**curl**
```
curl -X DELETE \
  http://localhost:8080/api/mock-order-api/customers/56f9e2a5-7649-45ec-a049-bdba99c5ad3a
```

**Sample Response**

If the request is successful, it will return an HTTP response `204`

`PATCH /orders/<orderId>/status/<status>`

Updates the `status` of an order record that matches the given `orderId`

**Sample Request**

```
curl -X PATCH \
  http://localhost:8080/api/mock-order-api/orders/56f9e2a5-7649-45ec-a049-bdba99c5ad3a/status/PENDING \
  -H 'Accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP response `200` with the response payload below:
```
{
    "result": "SUCCESS",
    "message": "Successfully changed status of order with id \"56f9e2a5-7649-45ec-a049-bdba99c5ad3a\" to PENDING."
}
```