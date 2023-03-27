# Robot Server API Documentation

## Introduction
The Robot Configuration Server provides an onsite or basic cloud service to host the configuration settings for a collection of Robot devices. It implements all the API functionality needed to interact with a Robot. The server can run standalone or provide a configuration only service while running a seperate third party transaction server.

---

## Conventions
1. Data is in JSON notation.
2. Values are always strings.
3. Booleans are always strings.
4. Timestamps are the last time the record was updated.
5. HTTP failures do have descriptive payloads.
6. By default data is sorted oldest to newest.

## Basic API Response Structure
The API follow a specific JSON response layout. Each packet always has *status*, *message*, *object* and *data* attributes.
- **status** has a single word describing the result of the http call. This can be different to the normal HTTP status. Status provides more insight into why an error occurred or even if there as only a warning.
- **message** provide insight into failures.
- **object** describes the data object
- **data** is a JSON object containing the relevant data

### Optional Attributes
A few optional attributes are provided when relevant to describe filtered data:
- **count** is the total number of records in the query set
- **pagecount** is provided when paging is requested. This is the total page count.
- **pagesize** is the number of records on the page
- **pagenum** is the number of the provided page


Example of a returned JSON object
```JSON
{
    "status": "SUCCESS",
    "message": "",
    "object": "CONTAINER-LIST",
    "count": "1",
    "data": [
        {
            "type": "PALLET",
            "cid": "101001101",
            "weight": "1000.00",
            "units": "kg",
            "status": "NORMAL",
            "timestamp": "2023-03-20 20:51:37"
        },
    ],
    "pagecount": "1",
    "pagesize": "20",
    "pagenum": "1"
}
```

## Container Store API
The container API provides methods to retrieve relevant data about containers. A container is anyhing that hold something relevant and can be uniquely identified. Examples are pallets, carton and bins. The API provides a few filtering options to narrow down relevant data.

<details><summary>Retrieve Container Data - Basic</summary>
To retreive a list of containers or a single container a single API endpint is used with the GET verb.
<p>

>GET http://server-ip/api/v1/store

```JSON
{
    "status": "SUCCESS",
    "message": "",
    "object": "CONTAINER-LIST",
    "count": "1",
    "data": [
        {
            "type": "PALLET",
            "cid": "101001101",
            "weight": "1000.00",
            "units": "kg",
            "status": "NORMAL",
            "timestamp": "2023-03-20 20:51:37"
        }
    ]
}
```
</p>
</details>

<details><summary>Filter Container Data - Timestamp</summary>
To retreive a list of containers between two timestamps it is needed to add query parameters to the URL. Both parameters are optional.
<p>

- **datestart** is the oldest date to filter from
- **dateend** is the latest date to filter to

### Examples
>GET http://server-ip/api/v1/store?datestart=2023-03-20%2020:51:37

>GET http://server-ip/api/v1/store?datestart=2023-03-20%2020:51:37&dateend=2023-03-20%2021:36:46

>GET http://server-ip/api/v1/store?dateend=2023-03-20%2020:51:37

```JSON
{
    "status": "SUCCESS",
    "message": "",
    "object": "CONTAINER-LIST",
    "count": "1",
    "data": [
        {
            "type": "PALLET",
            "cid": "101001101",
            "weight": "1000.00",
            "units": "kg",
            "status": "NORMAL",
            "timestamp": "2023-03-20 20:51:37"
        }
    ]
}
```
</p>
</details>

<details><summary>Filter Container Data - Paging</summary>
The API can provide paging for queried data.
<p>

- **page** is the page number requested
- **pagesize** is the number of records on a single page

If page is provide, the default pagesize is 20 records.

### Examples
>GET http://server-ip/api/v1/store?page=1

>GET http://server-ip/api/v1/store?page=1&pagesize=5

```JSON
{
    "status": "SUCCESS",
    "message": "",
    "object": "CONTAINER-LIST",
    "count": "8",
    "data": [
        {
            "type": "PALLET",
            "cid": "101001101",
            "weight": "1000.10",
            "units": "kg",
            "status": "NORMAL",
            "timestamp": "2023-03-20 20:51:37"
        },
        {
            "type": "PALLET",
            "cid": "1001001112",
            "weight": "1001.50",
            "units": "kg",
            "status": "NORMAL",
            "timestamp": "2023-03-20 21:35:57"
        },
        {
            "type": "PALLET",
            "cid": "10010011112",
            "weight": "1001.50",
            "units": "kg",
            "status": "NORMAL",
            "timestamp": "2023-03-20 21:36:45"
        },
        {
            "type": "PALLET",
            "cid": "1001120011112",
            "weight": "1001.50",
            "units": "kg",
            "status": "NORMAL",
            "timestamp": "2023-03-20 21:37:05"
        },
        {
            "type": "PALLET",
            "cid": "100111221212220011112",
            "weight": "1001.50",
            "units": "kg",
            "status": "OVERWRITE",
            "timestamp": "2023-03-20 21:59:52"
        }
    ],
    "pagecount": "2",
    "pagesize": "5",
    "pagenum": "1"
}
```

</p>
</details>

<details><summary>Filter Types of Containers</summary>
Data can be filtered according to container types.
<p>

- **type** specify partial match of container type name

### Examples
>GET http://server-ip/api/v1/store?type=PAL

>GET http://server-ip/api/v1/store?type=CART

```JSON
{
    "status": "SUCCESS",
    "message": "",
    "object": "CONTAINER-LIST",
    "count": "1",
    "data": [
        {
            "type": "PALLET",
            "cid": "101001101",
            "weight": "1000.00",
            "units": "kg",
            "status": "NORMAL",
            "timestamp": "2023-03-20 20:51:37"
        }
    ]
}
```

</p>
</details>

<details><summary>Ordering of Data</summary>
URL parameter to order data by barcode or timestamp.
<p>

- **ordering** specify ordering method, timestamp or barcode, descending or ascending order.

### Examples of Ordering
Timestamp Ascending
>GET http://server-ip/api/v1/store?ordering=timestamp

Timestamp Descending
>GET http://server-ip/api/v1/store?ordering=-timestamp

Barcode Ascending
>GET http://server-ip/api/v1/store?ordering=barcode

Barcode Descending
>GET http://server-ip/api/v1/store?ordering=-barcode

</p>
</details>

