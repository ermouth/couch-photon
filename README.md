# <img align="right" src="http://jquerymy.com/kod/photon-github.png" /> couch-photon
Futon-inspired CouchDB admin panel. Covers 100% of Futon and most of Fauxton features.

## Additional features

* High information density, similar or exceeding Futon
* Group operations over DBs: create, delete, set security and compact
* Advanced JSON editor, understanding both JS and JSON syntax
* Allows group file upload and renaming files before sending to CouchDB
* Instant full text search in view results and JSON docs
* Node-level and cluster config management.

See screencast at https://youtu.be/MHc6tozNhWU

## Installation is replication
__For CouchDB 1.6.1 and earlier.__ Create new doc in your `_replicator` DB and copy-paste below JSON into it. Save – and you are done.
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

## Another way to install
Download `photon.json` from [Github](https://raw.githubusercontent.com/ermouth/couch-photon/master/photon.json) or [AWS S3 CDN](https://s3-eu-west-1.amazonaws.com/cdn.cloudwall.me/photon/photon.json) and use one of the following ways:

a) Open JSON in any text editor. Create a doc in any CouchDB bucket, using Futon or Fauxton. Copy-paste JSON text into it. Save. Run `index.html`.

b) `curl -H 'Content-Type: application/json' -X PUT http://yourdomain.com:5984/somedb/_design/photon -d @photon.json`. Run `index.html`.

Next time you can upgrade Photon directly from Photon, clicking rightmost button on navbar and checking for updates at CDN.

## FAQ

__Where are source files?__

Photon never existed as source _files_. Its sources are CouchDB _docs_, and it is developed and built using specialized dev environment, [CloudWall](http://cloudwall.me).

__What is underlying technology?__

Photon employs most conservative and bullet-proof approaches whenever possible: jQuery plus established plugins to render UI, and XMLHttpRequest to interact with CouchDB. Photon contains no libs, originating from corporate OSS. 

__Why Photon uses Google fonts?__

Monospaced fonts are downloaded from Google webfonts to avoid inclusion large font attachments in Photon ddoc. One font family adds ~0.5–0.8Mb to ddoc size.

__Is it safe to update from CDN?__

Default CDN has logging turned off, so requests and IPs are not collected. Update is always explicit and never performed until you click the update link. 

---

(c) 2017 ermouth. Photon is MIT licensed.
