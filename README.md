## 🎓🔥 Scalable Indexing for Cassandra using DataStax Astra 🔥🎓

[![License Apache2](https://img.shields.io/hexpm/l/plug.svg)](http://www.apache.org/licenses/LICENSE-2.0)
[![Discord](https://img.shields.io/discord/685554030159593522)](https://discord.com/widget?id=685554030159593522&theme=dark)

![Storage Attached Index Workshop](https://user-images.githubusercontent.com/23346205/96910093-f5ee7100-146c-11eb-9cfb-06ea3732c2bd.png)

Welcome to the 'Scalable Indexing for Cassandra using DataStax Astra' workshop! In this two-hour workshop, the Developer Advocate team of DataStax will explain the new Storage Attached Indexing (SAI) feature using Astra, the cloud based Cassandra-as-a-Service platform delivered by DataStax, to demonstrate how you can use them to add some much wanted flexibility to your Cassandra data model by querying outside of primary key fields.

**To date, SAI is currently supported on DataStax Astra and DataStax Enterprise 6.8.3+. There is a currently a [CEP](https://cwiki.apache.org/confluence/display/CASSANDRA/CEP-7%3A+Storage+Attached+Index) to bring this functionality into Open Source Apache Cassandra.**

It doesn't matter if you join our workshop live or you prefer to do at your own pace, we have you covered. In this repository, you'll find everything you need for this workshop:

- Materials used during presentations
- Hands-on exercises
- Workshop videos
  - [First workshop](https://www.youtube.com/watch?v=GLJc1Uz9dqw) [NAM Time]
  - [Second workshop](https://www.youtube.com/watch?v=yNQYQjXtV30) [IST Time]
- [Discord chat](https://bit.ly/cassandra-workshop)
- [Questions and Answers](https://community.datastax.com/)

## Table of Contents

| Title  | Description
|---|---|
| **Slide deck** | [Slide deck for the workshop](slides/Presentation.pdf) |
| **Exercise Notebook** | [Exercises in Studio Notebook for Astra](SA_Index_Workshop.tar) |
| **SAI Documentation** | [SAI Documentation](https://docs.datastax.com/en/storage-attached-index/6.8/sai/saiQuickStart.html) |
| **1. Create your Astra instance** | [Create your Astra instance](#1-create-your-astra-instance) |
| **2. Getting started with SAI** | [Getting started with SAI](#2-getting-started-with-sai-storage-attached-index) |
| **3. IoT sensor data model use case** | [IoT sensor data model use case](#3-iot-sensor-data-model-use-case) |



## 1. Create your Astra instance

`ASTRA` service is available at url [https://astra.datastax.com](https://dtsx.io/workshop). `ASTRA` is the simplest way to run Cassandra with zero operations at all - just push the button and get your cluster. `Astra` offers **5 Gb Tier Free Forever** and you **don't need a credit card** or anything to sign-up and use it. 

**✅ Step 1a. Register (if needed) and Sign In to Astra** : You can use your `Github`, `Google` accounts or register with an `email`.

Make sure to chose a password with minimum 8 characters, containing upper and lowercase letters, at least one number and special character

- [Registration Page](https://dtsx.io/workshop)

![Registration Image](https://github.com/datastaxdevs/shared-assets/blob/master/astra/login-1000.png?raw=true)

- [Authentication Page](https://dtsx.io/workshop)

![Login Image](https://github.com/datastaxdevs/shared-assets/blob/master/astra/signin-raw.png?raw=true)


**✅ Step 1b. Choose the free plan and select your region**

![my-pic](https://github.com/datastaxdevs/shared-assets/blob/master/astra/choose-a-plan-1000-annotated.png?raw=true)

- **Select the free tier**: 5GB storage, no obligation

- **Select the region**: This is the region where your database will reside physically (choose one close to you or your users). For people in EMEA please use `europe-west-1` idea here is to reduce latency.

**✅ Step 1c. Configure and create your database**

You will find below which values to enter for each field.

![my-pic](https://github.com/datastaxdevs/shared-assets/blob/master/astra/create-and-configure-annotated-1000.png?raw=true)

- **Fill in the database name** - `sa_index_workshop_db.` While Astra allows you to fill in these fields with values of your own choosing, please follow our reccomendations to make the rest of the exercises easier to follow. If you don't, you are on your own! :)

- **Fill in the keyspace name** - `sa_index`. It's really important that you use the name sa_index here in order for all the exercises to work well. We realize you want to be creative, but please just roll with this one today.

- **Fill in the Database User name** - `index_user`. Note the user name is case-sensitive. Please use the case we suggest here.

- **Fill in the password** - `index_password1`. Fill in both the password and the confirmation fields. Note that the password is also case-sensitive. Please use the case we suggest here.

- **Create the database**. Review all the fields to make sure they are as shown, and click the `Create Database` button.

You will see your new database `pending` in the Dashboard.

![my-pic](https://github.com/datastaxdevs/shared-assets/blob/master/astra/dashboard-pending-1000.png?raw=true)

The status will change to `Active` when the database is ready, this will only take 2-3 minutes. You will also receive an email address when it is ready.

**✅ Step 1d. View your Database and connect**

Let’s review the database you have configured. Select your new database in the lefthand column.

Now you can select to connect, to park the database, to access CQL console or Studio.

![my-pic](https://github.com/datastaxdevs/shared-assets/blob/master/astra/summary-1000.png?raw=true)


[🏠 Back to Table of Contents](#table-of-contents)

## 2. Getting started with SAI (Storage Attached Index)
**SAI** is short for **Storage Attached Indexes**, it allows us to build indexes on Cassandra tables that dramatically improve the flexibility of Cassandra queries.

For a **non-technical introduction** to **SAI**, have a look at this [recent blog post](https://www.datastax.com/blog/get-your-head-clouds-part-1-3-build-cloud-native-apps-datastax-astra-dbaas-now-aws-gcp).

To learn more about **SAI** from a **technical perspective**, have a look at our [docs on SAI](https://docs.datastax.com/en/storage-attached-index/6.8/sai/saiQuickStart.html). Honestly, these docs are pretty great IMO especially the [SAI FAQ](https://docs.datastax.com/en/storage-attached-index/6.8/sai/saiFaqs.html). Definitely take a moment to read through these to get a better understanding of how all of this works and even more examples on top of what we are presenting in this repo.

Now, let's get into some examples. The first thing we'll need is a table and some data to work with. For that we need to talk about my dentist, or really, a contrived example of a client data model a dentist might need to use.

**✅ Step 2a. Navigate to the CQL Console and login to the database**

In the Summary screen for your database, select **_CQL Console_** from the top menu in the main window. This will take you to the CQL Console with a login prompt.

![astra cqlsh console](https://user-images.githubusercontent.com/23346205/97186856-35bc9d80-1778-11eb-80fd-df1a2f264a25.png)

Once you click the _`CQL Console`_ tab it will automatically log you in and present you with a `token@cqlsh>` prompt.


**✅ Step 2b. Describe keyspaces and USE `sa_index`**

Ok, you're logged in, and now we're ready to rock. Creating tables is quite easy, but before we create one we need to tell the database which keyspace we are working with.

First, let's **_DESCRIBE_** all of the keyspaces that are in the database. This will give us a list of the available keyspaces.

📘 **Command to execute**
```
desc KEYSPACES;
```
_"desc" is short for "describe", either is valid_

📗 **Expected output**

![desc keyspace output](https://user-images.githubusercontent.com/23346205/97188152-939db500-1779-11eb-8e47-0d6b4ebea74b.png)

Depending on your setup you might see a different set of keyspaces then in the image. The one we care about for now is **_sa_index_**. From here, execute the **_USE_** command with the **_sa_index_** keyspace to tell the database our context is within **_sa_index_**.

📘 **Command to execute**
```
use sa_index;
```

📗 **Expected output**

![use sa_index output](https://user-images.githubusercontent.com/23346205/97188451-e4151280-1779-11eb-98a2-f7292621f6ac.png)

Notice how the prompt displays `token@cqlsh:sa_index>` informing us we are **using** the **_sa_index_** keyspace. Now we are ready to create our tables.

**✅ Step 2c. Create a _`clients`_ table and insert some data**

Create the table.

📘 **Command to execute**

```SQL
CREATE TABLE IF NOT EXISTS clients (
    uniqueid uuid primary key,
    firstname text,
    lastname text,
    birthday date,
    nextappt timestamp,
    newpatient boolean,
    photo text
);
```

Insert some data into the table.

_We don't have real image URLs, so we're just using a placeholder string._

📘 **Commands to execute**
```SQL
INSERT INTO clients (uniqueid, firstname, lastname, birthday, nextappt, newpatient, photo) 
VALUES (D85745B1-4BEC-43D7-8B77-DD164CB9D1B8, 'Alice', 'Apple', '1984-01-24', '2020-10-20 12:00:00', true, 'imageurl');

INSERT INTO clients (uniqueid, firstname, lastname, birthday, nextappt, newpatient, photo) 
VALUES (2A4F139F-0BBF-4A6F-B982-5400F11D2F2B, 'Zeke', 'Apple', '1961-12-30', '2020-10-20 12:30:00', false, 'imageurl');

INSERT INTO clients (uniqueid, firstname, lastname, birthday, nextappt, newpatient, photo) 
VALUES (DF649261-89CB-446B-9998-FFA2D17506F9, 'Lorenzo', 'Banana', '1963-09-03', '2020-10-20 13:00:00', false, 'imageurl');

INSERT INTO clients (uniqueid, firstname, lastname, birthday, nextappt, newpatient, photo) 
VALUES (808E6BBF-A0F4-4E4C-9C97-E36751D51A8B, 'Miley', 'Banana', '1969-02-06', '2020-10-20 13:30:00', false, 'imageurl');

INSERT INTO clients (uniqueid, firstname, lastname, birthday, nextappt, newpatient, photo) 
VALUES (3D458A4D-2F54-4271-BEDC-1FC316B3CC96, 'Cheryl', 'Banana', '1970-07-11', '2020-10-20 14:00:00', false, 'imageurl');

INSERT INTO clients (uniqueid, firstname, lastname, birthday, nextappt, newpatient, photo) 
VALUES (287AB6B4-1AA6-45DF-B6F8-2BE253B9AACE, 'Red', 'Currant', '1974-02-18', '2020-10-20 15:00:00', false, 'imageurl');

INSERT INTO clients (uniqueid, firstname, lastname, birthday, nextappt, newpatient, photo) 
VALUES (AB49D151-CC04-40DC-AEEA-0A4E5F59D69A, 'Matthew', 'Durian', '1976-11-11', '2020-10-19 12:30:00', false, 'imageurl');

INSERT INTO clients (uniqueid, firstname, lastname, birthday, nextappt, newpatient, photo) 
VALUES (783CE790-16B4-4645-B27C-4FDF3994A755, 'Vanessa', 'Elderberry', '1977-12-03', '2020-10-20 15:30:00', false, 'imageurl');

INSERT INTO clients (uniqueid, firstname, lastname, birthday, nextappt, newpatient, photo) 
VALUES (D23997E4-CCCB-46BB-B92F-0D4582A68809, 'Elaine', 'Elderberry', '1979-11-16', '2020-10-20 10:00:00', true, 'imageurl');

INSERT INTO clients (uniqueid, firstname, lastname, birthday, nextappt, newpatient, photo) 
VALUES (36C386C1-3C3B-49FC-81B1-391D5537453D, 'Phoebe', 'Fig', '1986-01-27', '2020-10-21 11:00:00', false, 'imageurl');

INSERT INTO clients (uniqueid, firstname, lastname, birthday, nextappt, newpatient, photo) 
VALUES (00FEE7EE-8F93-4C2E-A8BE-3ADD81235822, 'Patricia', 'Grape', '1986-06-24', '2020-10-21 12:00:00', false, 'imageurl');

INSERT INTO clients (uniqueid, firstname, lastname, birthday, nextappt, newpatient, photo) 
VALUES (B9DB7E99-AD1C-49B1-97C6-87154663AEF4, 'Herb', 'Huckleberry', '1990-07-09', '2020-10-21 13:00:00', false, 'imageurl');

INSERT INTO clients (uniqueid, firstname, lastname, birthday, nextappt, newpatient, photo) 
VALUES (F4DB7673-CA4E-4382-BDCD-2C1704363590, 'John-Henry', 'Huckleberry', '1979-11-16', '2020-10-21 14:00:00', false, 'imageurl');

INSERT INTO clients (uniqueid, firstname, lastname, birthday, nextappt, newpatient, photo) 
VALUES (F4DB7673-CA4E-4382-BDCD-2C1704363595, 'Sven', 'Åskådare', '1967-11-07', '2020-10-21 14:00:00', false, 'imageurl');
```

**✅ Step 2d. Verify data exists**

Now let's take a look at the data we just inserted.

📘 **Command to execute**
```
SELECT * FROM clients;
```

📗 **Expected output**

![select from client table](https://user-images.githubusercontent.com/23346205/97189410-f2176300-177a-11eb-90ca-3604f113520f.png)

**✅ Step 2e. Create some indexes**

Ok great, we have data in our table, but remember we used **_`uniqueid`_** as our **primary key** when we created the table. If we want to query a single patient, we'd have to do that by the **_`uniqueid`_** column because that's our **partition key** _(don't forget, a single value in the primary key is always the partition key)_. 

As a matter of fact, let's try an example. Let's say I want to find a user by their lastname. 

📘 **Command to execute**
```SQL
SELECT * FROM clients WHERE lastname = 'Apple';
```
📗 **Expected output**

![clients where lastname allow filtering](https://user-images.githubusercontent.com/23346205/97208146-33b30880-1791-11eb-8470-13b6381de70e.png)

Right, the database is telling me here I **CANNOT** query against the **lastname** column because it is NOT in my primary key **_`uniqueid`_**.

But how would we search for users outside of using their unique ID's? We need to look for clients based on information they give us when they walk in the office. Namely, information like first and last name, or birthdate. Maybe a combination of those. Let's set up some indexes to do that.

_Don't worry about options in the below statements just yet. We'll get to that. For now, just execute the commands to create your indexes._

📘 **Commands to execute**
```SQL
CREATE CUSTOM INDEX IF NOT EXISTS ON clients(firstname) USING 'StorageAttachedIndex' 
WITH OPTIONS = {'case_sensitive': false, 'normalize': true };

CREATE CUSTOM INDEX IF NOT EXISTS ON clients(lastname) USING 'StorageAttachedIndex' 
WITH OPTIONS = {'case_sensitive': false, 'normalize': true };

CREATE CUSTOM INDEX IF NOT EXISTS ON clients(birthday) USING 'StorageAttachedIndex';
```

**✅ Step 2f. Execute queries that use firstname, lastname, and birthday using our indexes**

Remember, the **`clients`** table data model only includes **`uniqueid`** in the primary key. In the traditional Cassandra sense I can only query against the **`uniqueid`** column in the **WHERE** clause. However, with our **SAIndexes** now added we can do a lot more.

📘 **Command to execute**
```SQL
// Look for a client by ONLY their lastname. Notice the case used.
SELECT * FROM clients WHERE lastname = 'Apple';
```

📗 **Expected output**

![clients where lastname](https://user-images.githubusercontent.com/23346205/97198417-25f78600-1785-11eb-8632-9a7d30456ec4.png)

📘 **Command to execute**
```SQL
// Look for a client by their lastname and firstname. Notice the case used.
SELECT * FROM clients WHERE lastname = 'apple' AND firstname = 'alice';
```

📗 **Expected output**

![clients where firstname and lastname](https://user-images.githubusercontent.com/23346205/97198713-871f5980-1785-11eb-8416-bd595a70bf6d.png)

📘 **Command to execute**
```SQL
// Look for a client by an exact match to their birthday.
SELECT * FROM clients WHERE birthday = '1984-01-24';
```

📗 **Expected output**

![clients where birthday](https://user-images.githubusercontent.com/23346205/97198969-d9607a80-1785-11eb-8a5a-f754995634f1.png)

📘 **Command to execute**
```SQL
// Look for a client by a range match for the year of their birthday.
SELECT * FROM clients WHERE birthday > '1984-01-01' AND birthday < '1985-01-01';
```

📗 **Expected output**

![clients where birthday range](https://user-images.githubusercontent.com/23346205/97199744-baaeb380-1786-11eb-829d-5e05d99d4ba5.png)

📘 **Command to execute**
```SQL
// Look for a client by their firstname 
// and a range match for the year of their birthday. Again, notice the case used.
SELECT * FROM clients 
WHERE firstname = 'aLicE'
AND birthday > '1984-01-01' AND birthday < '1985-01-01';
```

📗 **Expected output**

![clients where name and birthday range](https://user-images.githubusercontent.com/23346205/97200012-0f522e80-1787-11eb-8aa9-d3c22049dd4b.png)

**✅ Step 2g. Digest everything we just did there**

Ok, so let's break that all down. I said earlier when we created the indexes I would explain the options included with some of the indexes. 
```SQL
WITH OPTIONS = {'case_sensitive': false, 'normalize': true };
```
So what does the **“WITH OPTIONS”** part mean? 

Well, [case_sensitive](https://docs.datastax.com/en/dse/6.8/cql/cql/cql_reference/cql_commands/cqlCreateCustomIndex.html#cqlCreateCustomIndex__cqlCreateCustomIndexOptions) is fairly straightforward. Setting this **false** allows us to match any combination of case for the terms we are querying against, **firstname** or **lastname** fields according to the indexes we created. 

This is why I kept varying the case used in our queries above. You could **NOT** have done does this with a traditional Cassandra query.

How about [normalize](https://docs.datastax.com/en/dse/6.8/cql/cql/cql_reference/cql_commands/cqlCreateCustomIndex.html#cqlCreateCustomIndex__cqlCreateCustomIndexOptions)? Basically, this means that special characters, like vowels with diacritics can be represented by multiple binary representations for the same character, which also makes things easier to match. 

An example would be a row with a column value that contained the character `Å (U+212B)`.  With **normalize** enabled a query that used the character `Å (U+00C5)` would find that row. This saves from the need to find all unicode variations for a single character.

📘 **Command to execute**

```SQL
SELECT * FROM clients WHERE lastname = 'Åskådare';
```

_To be clear, this is not Ascii folding where I might insert code that uses `é` and a select using `e`. This is coming as a future feature._

To sum up, we queried against a combination of string and date fields using exact matches, multiple string cases, and date ranges. Just by adding an index on 3 fields we significantly expanded the flexibility of our data model.

Let's do more.

**✅ Step 2h. Add another index to support a new data model requirement**

Imagine a case where we now have a requirement to find clients based off of their next appointment. 

Prior to **SAI**, if I wanted to accomplish this same thing in Cassandra, I would set up a new table using the **date** as the **partition key**, and I'd probably have the **appointment** slots as a **clustering column**, along with the **`uniqueid`** rounding out the primary key. 

Then, I would retrieve the days partition to get a list of the appointments for the day. Now, I have **two tables** that I need to worry about to support that query. 

Let's see what this looks like with **SAI**.

📘 **Command to execute**
```SQL
CREATE CUSTOM INDEX IF NOT EXISTS ON clients(nextappt) USING 'StorageAttachedIndex';
```

📘 **Command to execute**
```SQL
SELECT * FROM clients WHERE nextappt > '2020-10-20 09:00:00';
```

📗 **Expected output**

![clients where nextappt](https://user-images.githubusercontent.com/23346205/97206362-01081080-178f-11eb-8b7d-6b002da6f9fb.png)

[🏠 Back to Table of Contents](#table-of-contents)

## 3. IoT sensor data model use case
Time to swtich gears to a real IoT data model use case. 

In the following case, an organization recieved a feed of sensor data that always included all of the fields that the sensor kept track of, even if those fields hadn't changed since the last reading. 

**All of the data was sent whenever a single field changed values.**

Now, this isn't necessarily something that has to be difficult to deal with, if all of the key fields are the same, **we can easily overwrite redundant data without doing a read-before-write** and all of that non-changing, redundant data will just compact away.

But, a **problem arises** if we need to **query** based on **something other than the primary key**.

If this table is instead organized to be physically efficient for querying, then I may not be able to easily upsert the data from the sensor without 1) creating a lot of unnecessary records, or 2) being forced to do a read-before-write to check if a record already exists. 

In this case, the **user was stuck with option 1**, because **it was cheaper to store more data than it was to have the compute resources for all of that extra query power for the 20x read workload that was required just to check to see if a sensor reading was already in the database.**

For this section we will continue by using [this Studio notebook](SA_Index_Workshop.tar) in Astra. Please right-click on the link provided and choose **Copy Link Address**. From there, follow the instructions to import into Astra.

Click on the **`Studio`** tab within your Astra console.
![astra use studio](https://user-images.githubusercontent.com/23346205/97359835-c3c97e80-1873-11eb-82a7-2d6ce63d04f3.png)

This will launch **Studio** in a new tab.

Click the **`+`** icon to import a notebook.
![studio import notebooks](https://user-images.githubusercontent.com/23346205/97360192-3c303f80-1874-11eb-97c1-10d610c93f8c.png)

Choose **`IMPORT FROM URL`**.
![import from url](https://user-images.githubusercontent.com/23346205/97360504-ae088900-1874-11eb-94ca-1157295169de.png)

Paste in the link address you copied and click **`Import`**.
![import from url with link](https://user-images.githubusercontent.com/23346205/97360803-0dff2f80-1875-11eb-87a4-eb365d951969.png)

From there the notebook will be imported and opened automatically. Navigate down to Section **3. IoT sensor data model use case** to continue on.
![view studio notebook](https://user-images.githubusercontent.com/23346205/97361093-74844d80-1875-11eb-9d11-ceecc64dd1f7.png)

<!--
**✅ Step 3a. Create our `sensordata` table**

- This table will create a partition for every hour of every day for each location.
- In this case, we have between 1,000 and 10,000 locations, and there are potentially dozens of devices per location.
- The **`updated`** **STATIC** column will show the last time that the values in **`payload`** were updated that hour.
- As hours go by, we will naturally create a snapshot of the last hour.
- This might be undesirable if I didn't know that 
- I get at least one sensor payload per hour unless a device is offline.

📘 **Command to execute**
```SQL
CREATE TABLE sensordata (
    location text,
    dayhour timestamp,
    device_id text,
    device_name text,
    updated timestamp STATIC,
    payload map<text,text>,
    PRIMARY KEY ((location, dayhour), device_id)
) WITH CLUSTERING ORDER BY (device_id ASC);
```

**✅ Step 3b. Create indexes to address query needs outside of our primary key**

OK, so this table organizes the data the way we want it to be space efficient, and it gets rid of redundant records by virtue of the physical organziation that it creates on disk. 

We get the snapshot view that we want each hour, and if most of the values in “payload” don't change over the course an hour, then we avoid both having to do a read before write, and we avoid storing extra copies of that data. 

Where does **SAI** come into the picture? Well, the trick is that queries against this data use the non-unique **`device_name`** field along with the **`dayhour`** that we're looking for, but we also sometimes need to query by the key in the **`payload`** map. 

Being able to query with those inputs, and also organize the data as efficiently as we can is nearly impossible without **SAI**. 

Let's look at the indexes we need to make as well as load some sample data that we can query.

📘 **Commands to execute**
```SQL
CREATE CUSTOM INDEX IF NOT EXISTS ON sensordata(device_name) USING 'StorageAttachedIndex'
WITH OPTIONS = {'case_sensitive': false, 'normalize': true };

CREATE CUSTOM INDEX IF NOT EXISTS ON sensordata(dayhour) USING 'StorageAttachedIndex';

CREATE CUSTOM INDEX IF NOT EXISTS ON sensordata(keys(payload)) USING 'StorageAttachedIndex' 
WITH OPTIONS = {'case_sensitive': false, 'normalize': true };
```

_That last **CREATE CUSTOM INDEX** command uses the [keys()](https://docs.datastax.com/en/storage-attached-index/6.8/sai/saiUsing.html#SAIcollectionmapexampleswithkeys,values,andentries) function to index only the map keys in the **payload** map. That lets us **search for entries** with a **specific key name**, which in this case allows us to query for a particular sensor reading._ 

So, now that we have our table structure, let's load some data and query it.

**✅ Step 3c. Insert data**

_Note that the UUIDs here are only increasing by one because it's an expedient thing to do when manually generating data, in the real world, don't do that._

📘 **Commands to execute**
```SQL
INSERT INTO sensordata(location, dayhour, device_id, device_name, updated, payload)
VALUES('ABC','2020-10-20 01:00:00','87C5EFE5-1849-4C0B-BBCD-F4FB84F6FB78','device1','2020-10-20 01:30:00',{'temp':'freezing!', 'humidity':'low'});

INSERT INTO sensordata(location, dayhour, device_id, device_name, updated, payload)
VALUES('ABC','2020-10-20 01:00:00','87C5EFE5-1849-4C0B-BBCD-F4FB84F6FB79','device1','2020-10-20 01:31:00',{'temp':'freezing!', 'humidity':'low', 'mood':'hungry'});

INSERT INTO sensordata(location, dayhour, device_id, device_name, updated, payload)
VALUES('ABC','2020-10-20 01:00:00','87C5EFE5-1849-4C0B-BBCD-F4FB84F6FB79','device1','2020-10-20 01:32:00',{'temp':'freezing!', 'humidity':'low', 'mood':'full'});

INSERT INTO sensordata(location, dayhour, device_id, device_name, updated, payload)
VALUES('ABC','2020-10-20 02:00:00','87C5EFE5-1849-4C0B-BBCD-F4FB84F6FB80','device2','2020-10-20 02:30:00',{'speed':'stopped', 'color':'blue'});

INSERT INTO sensordata(location, dayhour, device_id, device_name, updated, payload)
VALUES('ABC','2020-10-20 03:00:00','87C5EFE5-1849-4C0B-BBCD-F4FB84F6FB81','device2','2020-10-20 03:30:00',{'speed':'slow', 'color':'blue'});

INSERT INTO sensordata(location, dayhour, device_id, device_name, updated, payload)
VALUES('DEF','2020-10-20 00:00:00','87C5EFE5-1849-4C0B-BBCD-F4FB84F6FB82','dev1','2020-10-20 00:30:00',{'temp':'hot!', 'humidity':'sticky'});

INSERT INTO sensordata(location, dayhour, device_id, device_name, updated, payload)
VALUES('DEF','2020-10-20 01:00:00','87C5EFE5-1849-4C0B-BBCD-F4FB84F6FB83','dev2','2020-10-20 01:30:00',{'temp':'warm', 'humidity':'muggy'});

INSERT INTO sensordata(location, dayhour, device_id, device_name, updated, payload)
VALUES('DEF','2020-10-20 02:00:00','87C5EFE5-1849-4C0B-BBCD-F4FB84F6FB84','dev3','2020-10-20 02:30:00',{'temp':'freezing!', 'humidity':'my beard is growing icicles'});

INSERT INTO sensordata(location, dayhour, device_id, device_name, updated, payload)
VALUES('GHI','2020-10-20 00:00:00','87C5EFE5-1849-4C0B-BBCD-F4FB84F6FB85','doohickey','2020-10-20 00:30:00',{'temp':'hot!', 'humidity':'dry', 'orientation':'rightside up'});

INSERT INTO sensordata(location, dayhour, device_id, device_name, updated, payload)
VALUES('GHI','2020-10-20 01:00:00','87C5EFE5-1849-4C0B-BBCD-F4FB84F6FB86','doohickey','2020-10-20 01:30:00',{'temp':'hot!', 'humidity':'dry', 'orientation':'upside down'});

INSERT INTO sensordata(location, dayhour, device_id, device_name, updated, payload)
VALUES('GHI','2020-10-20 02:00:00','87C5EFE5-1849-4C0B-BBCD-F4FB84F6FB87','doohickey','2020-10-20 02:30:00',{'temp':'hot!', 'humidity':'dry', 'orientation':'forwards'});

INSERT INTO sensordata(location, dayhour, device_id, device_name, updated, payload)
VALUES('GHI','2020-10-20 03:00:00','87C5EFE5-1849-4C0B-BBCD-F4FB84F6FB85','doohickey','2020-10-20 03:30:00',{'temp':'hot!', 'humidity':'dry', 'orientation':'backwards'});

INSERT INTO sensordata(location, dayhour, device_id, device_name, updated, payload)
VALUES('ABC','2020-10-20 03:00:00','87C5EFE5-1849-4C0B-BBCD-F4FB84F6FB78','device1','2020-10-20 03:31:00',{'temp':'freezing!', 'humidity':'low', 'mood':'hungry again'});

INSERT INTO sensordata(location, dayhour, device_id, device_name, updated, payload)
VALUES('ABC','2020-10-20 03:00:00','87C5EFE5-1849-4C0B-BBCD-F4FB84F6FB78','device1','2020-10-20 03:35:00',{'temp':'freezing!', 'humidity':'low', 'mood':'full'});

INSERT INTO sensordata(location, dayhour, device_id, device_name, updated, payload)
VALUES('ABC','2020-10-20 03:00:00','87C5EFE5-1849-4C0B-BBCD-F4FB84F6FB78','device1','2020-10-20 03:40:00',{'temp':'freezing!', 'humidity':'low', 'mood':'no, still peckish'});

INSERT INTO sensordata(location, dayhour, device_id, device_name, updated, payload)
VALUES('DEF','2020-10-20 03:00:00','87C5EFE5-1849-4C0B-BBCD-F4FB84F6FB93','dev4','2020-10-20 03:30:00',{'temp':'/tmp', 'speed':'low'});

INSERT INTO sensordata(location, dayhour, device_id, device_name, updated, payload)
VALUES('DEF','2020-10-20 03:00:00','87C5EFE5-1849-4C0B-BBCD-F4FB84F6FB93','dev4','2020-10-20 03:40:00',{'temp':'/var/lib/tmp', 'speed':'low', 'color':'green'});

INSERT INTO sensordata(location, dayhour, device_id, device_name, updated, payload)
VALUES('DEF','2020-10-20 03:00:00','87C5EFE5-1849-4C0B-BBCD-F4FB84F6FB94','dev5','2020-10-20 03:45:00',{'temp':'freezing!', 'humidity':'low', 'mood':'hungry'});

INSERT INTO sensordata(location, dayhour, device_id, device_name, updated, payload)
VALUES('DEF','2020-10-20 03:00:00','87C5EFE5-1849-4C0B-BBCD-F4FB84F6FB95','dev6','2020-10-20 03:50:00',{'temp':'freezing!', 'humidity':'low', 'mood':'hungry'});
```

_It is worth pointing out that doing an **INSERT** on a **Map** column like this will **always replace the full map**. In this case, I know that's OK for my use case because I always get a full input file that has all the sensor readings in it. Sometimes, this isn't what you want, and you'll need to use the **SET** keyword to **set a specific value in the map**._

**✅ Step 3d. Execute queries that use device_name, dayhour, and payload map keys using our indexes**

📘 **Command to execute**
```SQL
SELECT * FROM sensordata WHERE device_name='doohickey';
```

📗 **Expected output**

![sensordata where device_name](https://user-images.githubusercontent.com/23346205/97318153-f2c6fc80-1841-11eb-8c77-9aefed902d61.png)

📘 **Command to execute**
```SQL
SELECT * FROM sensordata 
WHERE device_name = 'device1' 
AND dayhour = '2020-10-20 01:00:00';
```

📗 **Expected output**

![sensordata where device_name](https://user-images.githubusercontent.com/23346205/97318153-f2c6fc80-1841-11eb-8c77-9aefed902d61.png)

-->