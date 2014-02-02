---
layout: post
description:
  A performance comparison between PouchDB and Couchbase Lite.
  Both are suitable for building complex (web) apps and they each have their strengths.
title: PouchDB vs. Couchbase Lite - a performance review
---

Client side databases with offline support are very important for mobile hybrid apps where performance matters. I've developed several
apps with Cordova/Phonegap in the last months and always needed a database to save information on the client, that has offline
support and allows replicating data back to my cloud database. Two databases came up whenever I was looking for a solution - PouchDB and
Couchbase Lite Phonegap. PouchDB works entirely in the browser whereas Couchbase Lite works with native databases on iOS and Android
through a JavaScript API.

Today I'd like to draw a comparison between them focusing on performance. How fast do they write data and how fast do they
retrieve data? I chose four actions and did ten repetitions each

 - bulk add documents
 - get all documents
 - get document by id
 - query database via view
 
The code is on [GitHub](https://github.com/zeMirco/pouchdb-vs-couchbase) so you can run the tests for yourself. My tests were done on my MacBook Pro running Mavericks
 inside the iOS emulator. Therefore the exact values will be different depending on which hardware you use. The overall results and findings should
 still be the same though.

### Bulk add documents

The first action is adding random documents to each database. The first run was with 10000 and the second with 25000 documents.

The code snippets look like this:

```js
// add array of documents to PouchDB
function pouch_bulkAdd() {
  var max = 10000;
  var data = generate(max);
  console.time('PouchDB - bulk add');
  db.bulkDocs({docs: data}, function(err, res) {
    if (err) console.log(err);
    console.timeEnd('PouchDB - bulk add');
    // save one user for later query
    dummyId = res[5000].id;
  })
}
```

```js
// add array of documents to Couchbase Lite
function couchbase_bulkAdd() {
  var max = 10000;
  var data = generate(max);
  console.time('Couchbase lite - bulk add');
  $.ajax({
    type: 'POST',
    url: globalUrl + 'performance/_bulk_docs',
    data: JSON.stringify({'docs': data}),
    contentType : 'application/json'
  })
  .done(function(res) {
    console.timeEnd('Couchbase lite - bulk add');
    dummyId = res[5000].id;
  });
}
```

<table class="table table-condensed full-width">
  <thead>
    <tr>
      <th>#</th>
      <th>PouchDB (10000 docs)</th>
      <th>CB Lite (10000 docs)</th>
      <th> - </th>
      <th>PouchDB (25000 docs)</th>
      <th>CB Lite (25000 docs)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>2214 ms</td>
      <td>2459 ms</td>
      <td> - </td>
      <td>5568 ms</td>
      <td>6064 ms</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2391 ms</td>
      <td>2777 ms</td>
      <td> - </td>
      <td>5561 ms</td>
      <td>6583 ms</td>
    </tr>
    <tr>
      <td>3</td>
      <td>1532 ms</td>
      <td>2966 ms</td>
      <td> - </td>
      <td>5494 ms</td>
      <td>6683 ms</td>
    </tr>
    <tr>
      <td>4</td>
      <td>1506 ms</td>
      <td>3071 ms</td>
      <td> - </td>
      <td>5805 ms</td>
      <td>6723 ms</td>
    </tr>
    <tr>
      <td>5</td>
      <td>1539 ms</td>
      <td>3022 ms</td>
      <td> - </td>
      <td>5705 ms</td>
      <td>6828 ms</td>
    </tr>
    <tr>
      <td>6</td>
      <td>1607 ms</td>
      <td>3087 ms</td>
      <td> - </td>
      <td>5746 ms</td>
      <td>6769 ms</td>
    </tr>
    <tr>
      <td>7</td>
      <td>1596 ms</td>
      <td>3141 ms</td>
      <td> - </td>
      <td>5841 ms</td>
      <td>6805 ms</td>
    </tr>
    <tr>
      <td>8</td>
      <td>1694 ms</td>
      <td>3239 ms</td>
      <td> - </td>
      <td>5676 ms</td>
      <td>6887 ms</td>
    </tr>
    <tr>
      <td>9</td>
      <td>1600 ms</td>
      <td>3245 ms</td>
      <td> - </td>
      <td>5820 ms</td>
      <td>6923 ms</td>
    </tr>
    <tr>
      <td>10</td>
      <td>1579 ms</td>
      <td>3278 ms</td>
      <td> - </td>
      <td>5873 ms</td>
      <td>6886 ms</td>
    </tr>
  </tbody>
  <tfoot>
    <tr>
      <td>Ø</td>
      <td style="background-color: lime;">1725.8 ms</td>
      <td>3028.5 ms</td>
      <td> - </td>
      <td style="background-color: lime;">5708.9 ms</td>
      <td>6715.1 ms</td>
    </tr>
  </tfoot>
</table>

![bulk add documents](https://s3.amazonaws.com/zeMirco/github/pouchdb-vs-couchbase/bulk-add.png)

### Get all documents

The second action was getting all documents from each database, again with 10000 documents and then with 25000 documents.

```js
// get all documents from PouchDB
function pouch_allDocs() {
  console.time('PouchDB - all docs');
  db.allDocs(function(err, res) {
    if (err) log(err);
    console.timeEnd('PouchDB - all docs');
  });
}
```
 
```js
// get all documents from Couchbase Lite
function couchbase_allDocs() {
  console.time('Couchbase lite - all docs');
  $.get(globalUrl + 'performance/_all_docs')
  .done(function(res) {
    console.time('Couchbase lite - all docs');
  });
}
```

<table class="table table-condensed full-width">
  <thead>
    <tr>
      <th>#</th>
      <th>PouchDB (10000 docs)</th>
      <th>CB Lite (10000 docs)</th>
      <th> - </th>
      <th>PouchDB (25000 docs)</th>
      <th>CB Lite (25000 docs)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>345 ms</td>
      <td>126 ms</td>
      <td> - </td>
      <td>852 ms</td>
      <td>364 ms</td>
    </tr>
    <tr>
      <td>2</td>
      <td>325 ms</td>
      <td>29 ms</td>
      <td> - </td>
      <td>796 ms</td>
      <td>41 ms</td>
    </tr>
    <tr>
      <td>3</td>
      <td>315 ms</td>
      <td>16 ms</td>
      <td> - </td>
      <td>800 ms</td>
      <td>40 ms</td>
    </tr>
    <tr>
      <td>4</td>
      <td>315 ms</td>
      <td>16 ms</td>
      <td> - </td>
      <td>782 ms</td>
      <td>39 ms</td>
    </tr>
    <tr>
      <td>5</td>
      <td>311 ms</td>
      <td>16 ms</td>
      <td> - </td>
      <td>806 ms</td>
      <td>39 ms</td>
    </tr>
    <tr>
      <td>6</td>
      <td>314 ms</td>
      <td>17 ms</td>
      <td> - </td>
      <td>791 ms</td>
      <td>39 ms</td>
    </tr>
    <tr>
      <td>7</td>
      <td>315 ms</td>
      <td>22 ms</td>
      <td> - </td>
      <td>791 ms</td>
      <td>40 ms</td>
    </tr>
    <tr>
      <td>8</td>
      <td>308 ms</td>
      <td>18 ms</td>
      <td> - </td>
      <td>787 ms</td>
      <td>39 ms</td>
    </tr>
    <tr>
      <td>9</td>
      <td>315 ms</td>
      <td>16 ms</td>
      <td> - </td>
      <td>821 ms</td>
      <td>37 ms</td>
    </tr>
    <tr>
      <td>10</td>
      <td>313 ms</td>
      <td>16 ms</td>
      <td> - </td>
      <td>801 ms</td>
      <td>40 ms</td>
    </tr>
  </tbody>
  <tfoot>
    <tr>
      <td>Ø</td>
      <td>317.6 ms</td>
      <td style="background-color: lime;">29.2 ms</td>
      <td> - </td>
      <td>802.7 ms</td>
      <td style="background-color: lime;">71.8 ms</td>
    </tr>
  </tfoot>
</table>

![get all documents](https://s3.amazonaws.com/zeMirco/github/pouchdb-vs-couchbase/get-all-docs.png)

### Get document by id

The third action was getting a single document by id. First one document was picked out of the 10000 and for the second run
out of 25000 documents.

```js
// get a certain document by id
function pouch_get() {
  console.time('PouchDB - get single doc');
  db.get(dummyId, function(err, doc) {
    if (err) console.log(err);
    console.timeEnd('PouchDB - get single doc');
    queryUser = doc;
  });
}
```

```js
// get a certain document by id
function couchbase_get() {
  console.time('Couchbase lite - get single doc');
  $.get(globalUrl + 'performance/' + dummyId)
  .done(function(doc) {
    console.timeEnd('Couchbase lite - get single doc');
    queryUser = doc;
  });
}
```

<table class="table table-condensed full-width">
  <thead>
    <tr>
      <th>#</th>
      <th>PouchDB (10000 docs)</th>
      <th>CB Lite (10000 docs)</th>
      <th> - </th>
      <th>PouchDB (25000 docs)</th>
      <th>CB Lite (25000 docs)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>5 ms</td>
      <td>3 ms</td>
      <td> - </td>
      <td>8 ms</td>
      <td>8 ms</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2 ms</td>
      <td>4 ms</td>
      <td> - </td>
      <td>2 ms</td>
      <td>4 ms</td>
    </tr>
    <tr>
      <td>3</td>
      <td>2 ms</td>
      <td>4 ms</td>
      <td> - </td>
      <td>3 ms</td>
      <td>5 ms</td>
    </tr>
    <tr>
      <td>4</td>
      <td>3 ms</td>
      <td>4 ms</td>
      <td> - </td>
      <td>3 ms</td>
      <td>5 ms</td>
    </tr>
    <tr>
      <td>5</td>
      <td>2 ms</td>
      <td>4 ms</td>
      <td> - </td>
      <td>3 ms</td>
      <td>3 ms</td>
    </tr>
    <tr>
      <td>6</td>
      <td>3 ms</td>
      <td>3 ms</td>
      <td> - </td>
      <td>4 ms</td>
      <td>4 ms</td>
    </tr>
    <tr>
      <td>7</td>
      <td>3 ms</td>
      <td>3 ms</td>
      <td> - </td>
      <td>3 ms</td>
      <td>7 ms</td>
    </tr>
    <tr>
      <td>8</td>
      <td>2 ms</td>
      <td>5 ms</td>
      <td> - </td>
      <td>3 ms</td>
      <td>4 ms</td>
    </tr>
    <tr>
      <td>9</td>
      <td>2 ms</td>
      <td>4 ms</td>
      <td> - </td>
      <td>3 ms</td>
      <td>4 ms</td>
    </tr>
    <tr>
      <td>10</td>
      <td>2 ms</td>
      <td>3 ms</td>
      <td> - </td>
      <td>4 ms</td>
      <td>5 ms</td>
    </tr>
  </tbody>
  <tfoot>
    <tr>
      <td>Ø</td>
      <td style="background-color: lime;">2.6 ms</td>
      <td>3.7 ms</td>
      <td> - </td>
      <td style="background-color: lime;">3.6 ms</td>
      <td>4.9 ms</td>
    </tr>
  </tfoot>
</table>

![get document by id](https://s3.amazonaws.com/zeMirco/github/pouchdb-vs-couchbase/get-doc-by-id.png)

### Query db with view document and key

When you run queries against each database you have to remember that Couchbase Lite needs view documents (like CouchDB)
compared to PouchDB that works with on-the-fly map/reduce functions. So whenever we initialize our app we have to save all
required view documents to Couchbase Lite (or check if they are already present). As you can see the mapping function is the
same for both databases.

```js
// couchbase lite view
var view = {
  "id": "_design/users",
  "views": {
    "email": {
      "map": "function(doc) { emit(doc.email, null) }"
    }
  }
};

// save view to couchbase lite
$.ajax({
  type: 'PUT',
  url: globalUrl + 'performance/_design/users',
  data: JSON.stringify(view),
  contentType : 'application/json'
})
.done(function(res) {
  log('view created');
});
```

Now you can query each database.

```js
// query pouchdb
function pouch_query() {
  // mapping function
  var map = function(doc) {
    emit(doc.email, null);
  };
  console.time('PouchDB - query single doc');
  // query PouchDB
  db.query({map: map}, {key: queryUser.email}, function(err, res) {
    if (err) console.log(err);
    console.timeEnd('PouchDB - query single doc');
  });
}
```

```js
// query the database
function couchbase_query() {
  console.time('Couchbase lite - query single doc');
  $.ajax({
    url: globalUrl + 'performance/_design/users/_view/email?key="' + queryUser.email + '"'
  })
  .done(function(doc) {
    console.timeEnd('Couchbase lite - query single doc');
  });
}
```

<table class="table table-condensed full-width">
  <thead>
    <tr>
      <th>#</th>
      <th>PouchDB (10000 docs)</th>
      <th>CB Lite (10000 docs)</th>
      <th> - </th>
      <th>PouchDB (25000 docs)</th>
      <th>CB Lite (25000 docs)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>370 ms</td>
      <td>1754 ms</td>
      <td> - </td>
      <td>859 ms</td>
      <td>4401 ms</td>
    </tr>
    <tr>
      <td>2</td>
      <td>338 ms</td>
      <td>4 ms</td>
      <td> - </td>
      <td>846 ms</td>
      <td>5 ms</td>
    </tr>
    <tr>
      <td>3</td>
      <td>324 ms</td>
      <td>5 ms</td>
      <td> - </td>
      <td>842 ms</td>
      <td>4 ms</td>
    </tr>
    <tr>
      <td>4</td>
      <td>338 ms</td>
      <td>3 ms</td>
      <td> - </td>
      <td>873 ms</td>
      <td>7 ms</td>
    </tr>
    <tr>
      <td>5</td>
      <td>346 ms</td>
      <td>3 ms</td>
      <td> - </td>
      <td>866 ms</td>
      <td>3 ms</td>
    </tr>
    <tr>
      <td>6</td>
      <td>340 ms</td>
      <td>3 ms</td>
      <td> - </td>
      <td>862 ms</td>
      <td>4 ms</td>
    </tr>
    <tr>
      <td>7</td>
      <td>337 ms</td>
      <td>3 ms</td>
      <td> - </td>
      <td>838 ms</td>
      <td>4 ms</td>
    </tr>
    <tr>
      <td>8</td>
      <td>330 ms</td>
      <td>6 ms</td>
      <td> - </td>
      <td>861 ms</td>
      <td>4 ms</td>
    </tr>
    <tr>
      <td>9</td>
      <td>332 ms</td>
      <td>4 ms</td>
      <td> - </td>
      <td>859 ms</td>
      <td>3 ms</td>
    </tr>
    <tr>
      <td>10</td>
      <td>337 ms</td>
      <td>3 ms</td>
      <td> - </td>
      <td>844 ms</td>
      <td>7 ms</td>
    </tr>
  </tbody>
  <tfoot>
    <tr>
      <td>Ø</td>
      <td>339.2 ms</td>
      <td style="background-color: lime;">178.8 ms</td>
      <td> - </td>
      <td>855 ms</td>
      <td style="background-color: lime;">444.2 ms</td>
    </tr>
  </tfoot>
</table>

![query via view document](https://s3.amazonaws.com/zeMirco/github/pouchdb-vs-couchbase/query-via-view.png)

### Conclusion

Here is a summary of the results. For the part "query doc via view" I'll call it a draw. Couchbase Lite is about 5 times
slower than PouchDB but has the huge advantage of caching for subsequent requests (and therefore wins when averaging the request
times).

<table class="table table-condensed full-width">
  <thead>
    <tr>
      <td></td>
      <td style="text-align: center;">PouchDB</td>
      <td style="text-align: center;">Couchbase Lite Phonegap</td>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>bulk add documents</td>
      <td style="text-align: center;">+</td>
      <td></td>
    </tr>
    <tr>
      <td>get all documents</td>
      <td></td>
      <td style="text-align: center;">+</td>
    </tr>
    <tr>
      <td>get document by id</td>
      <td style="text-align: center;">+</td>
      <td></td>
    </tr>
    <tr>
      <td>query doc via view</td>
      <td style="text-align: center;">o</td>
      <td style="text-align: center;">o</td>
    </tr>
  </tbody>
</table>

PouchDB (and CouchDB) are [different](http://www.couchbase.com/couchbase-vs-couchdb) from Couchbase Lite (and Couchbase). Jan Lehnardt and
 Scott Hanselman have a great discussion about that topic in the podcast [Understanding CouchDB and NoSQL with Jan Lehnardt](http://www.hanselminutes.com/386/understanding-couchdb-and-nosql-with-jan-lehnardt).
 The most important difference is caching. Couchbase is a combination between CouchDB and Membase and as it is stated on their homepage
  "has a built-in, memcached-based caching technology". The data tells the same story.
  
Take a look at the last section "Query db with view document and key". On the first request Couchbase Lite needs much more
time than PouchDB to find the document. However on subsequent requests Couchbase Lite is able to find the same document
within a few milliseconds whereas PouchDB constantly needs the same amount of time. That means you should always try to 
guess which documents the user is looking for before the user actually wants to get them. That way a requests only takes a few
milliseconds and your app delivers a great user experience. The caching mechanism is also clearly noticeable when getting all
documents from the db. The first requests takes much longer than the following ones. However compared to PouchDB all requests, 
even the first one, are much faster.

PouchDB wins the first section where we add a huge amount of random data to the db. Finding a document by ID is also faster in PouchDB
compared to Couchbase Lite. Interesting here is that Couchbase Lite doesn't seem to cache this requests. Subsequent requests aren't 
significantly faster than the first one and PouchDB still wins after a couple of repeated requests.

In total it comes down to the caching feature of Couchbase Lite. PouchDB handles writing documents to db, where caching has no influence,
 better whereas Couchbase Lite is much faster, especially after the first requests, when it comes to reading documents from db. 
 Therefore it is up to you and your requirements, which database you choose for your next project.
 
There is also a new kid in town - [http://dev.yathit.com/](http://dev.yathit.com/). I haven't yet tried
this one and cannot say anything about performance and overall usability. It has one major disadvantage compared to PouchDB and
Couchbase Lite and that is replication / syncing. That feature is not built into the db and you have to implement it yourself.