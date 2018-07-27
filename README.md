# <img align="right" src="http://jquerymy.com/kod/photon-github.png" /> couch-photon
Photon is Futon-inspired CouchDB admin panel. Covers 100% of Futon and most of Fauxton features. Photon is a single CouchDB design document with attachments, so it can be installed on any CouchDB without re-building or reconfiguring CouchDB itself.

Photon is completely self-contained and is ok for restricted networks.

## Additional features

* High information density, similar or exceeding Futon
* Group operations with DBs: create, delete, set security or compact
* Group operations with docs: selective replication to other DB and deletion
* Group \_replicator operations: selective stop and restart of sync tasks 
* Advanced JSON editor, understanding both JS and JSON syntax
* Allows group file upload and renaming files before sending to CouchDB
* Supports Mango queries and indexes
* Instant full text search in view results and JSON docs
* Log viewer with instant search
* Node and cluster level config management.

See screencast at https://youtu.be/MHc6tozNhWU

## Installation using curl

1. Create a `photon` database, providing credentials: `curl -H "Content-Type: application/json" -X PUT http://admin:____@127.0.0.1:5984/photon`
2. Fetch the source code and put it into a design document, providing credentials: `curl https://raw.githubusercontent.com/ermouth/couch-photon/master/photon.json | curl -H "Content-Type: application/json" -X PUT http://admin:____@127.0.0.1:5984/photon/_design/photon -d @-`.
3. Open `http://127.0.0.1:5984/photon/_design/photon/index.html` in the browser. 

Next time you can upgrade Photon directly from Photon itself. Just click the rightmost button at the navbar and check for updates.

## Installation using copy/paste
Download `photon.json` from [Github](https://raw.githubusercontent.com/ermouth/couch-photon/master/photon.json) or [AWS S3 CDN](https://s3-eu-west-1.amazonaws.com/cdn.cloudwall.me/photon/photon.json) and use one of the following ways:

a) Open JSON in any text editor. Create a doc in any CouchDB bucket, using Futon or Fauxton. Copy-paste JSON text into it. Save. Run `index.html` attachment, clicking it in Futon or Fauxton, or directly typing smth like `yourcouchdomain.xyz/db_with_photon/_design/photon/index.html`.

b) `curl -H 'Content-Type: application/json' -X PUT http://yourdomain.com:5984/somedb/_design/photon -d @photon.json`. Run `index.html`.

Next time you can upgrade Photon directly from Photon itself. Just click the rightmost button at the navbar and check for updates.

## Installation using replication
You can install Photon using native CouchDB replication. Since DB you will replicate from is of very limited capacity, please, only replicate once, do not make sync continuous.

__For CouchDB 1.7.2 and earlier.__ Create new doc in your `_replicator` DB and copy-paste below JSON into it. Save – and you are done.
```json
{
  "_id": "Photon",
  "source": "https://cloudwall.smileupps.com/photon/",
  "target": "photon",
  "create_target":true,
  "doc_ids":["_design/photon"],
  "user_ctx":{"name":"admin", "roles":["_admin"]}
}
```
To make sure `user_ctx` section works properly, you must have CouchDB proxy auth turned on. By default it’s active in CouchDB 1.x.

__For CouchDB 2.x__ JSON is bit different (see below). You need to insert credentials since 2.x does not understand `user_ctx` param.
```json
{
  "_id": "Photon",
  "source": "https://cloudwall.smileupps.com/photon/",
  "target": "http://admin:__________@localhost:5984/photon",
  "create_target":true,
  "doc_ids":["_design/photon"]
}
```

Next time you can upgrade Photon directly from Photon itself, without repliction. Just click the rightmost button at the navbar and check for updates.

## Dedicated host

Photon can run as a couchapp from a dedicated domain. To set up Photon for a dedicated host, 
you only need to configure CouchDB properly. First, [set up CORS](https://cloudwall.me/docs/sync.html#h-1co7nyhc) 
for the specified domain. Then set up two config keys:
```
[vhosts] 
photon.mydomain.xyz = /photon/_design/photon/_rewrite
[httpd]
secure_rewrites = false
```
Now typing `photon.mydomain.xyz` in browser runs Photon.

## FAQ

__Where are source files?__

Photon never existed as source _files_. Its sources are CouchDB _docs_, and it is developed and built using specialized dev environment, [CloudWall](http://cloudwall.me).

__What is underlying technology?__

Photon employs most conservative and bullet-proof approaches whenever possible: jQuery plus established plugins to render UI, and XMLHttpRequest to interact with CouchDB. Photon contains no libs, originating from corporate OSS. 

__Why Photon uses external fonts?__

Since version 1.2 it doesn’t. All fonts and icons are attached to the Photon design document.

__Is it safe to update from CDN?__

Default CDN has logging turned off, so requests and IPs are not collected. Update is always explicit and never performed until you click the update link. 

---

(c) 2018 ermouth. Photon is MIT licensed.
