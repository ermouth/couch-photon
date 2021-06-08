# <img align="right" src="http://jquerymy.com/kod/photon-github.png" /> couch-photon
Photon is an alternative CouchDB admin panel with all Futon and most of Fauxton features. Photon is a single CouchDB design document with attachments, so it can be installed on any running CouchDB without rebuilding or reconfiguring.

Basic Photon features are shown in a short screencast at [youtu.be/M9ptWXfwMN8](https://youtu.be/M9ptWXfwMN8).

Photon is completely self-contained and is safe for restricted networks. Unless configured [differently](#configuring-access), users must have `_admin` or `app-photon` role to use Photon; this restriction only protects the app, it doesn’t add more security to CouchDB itself.

## Unique features

* [Group dump to ZIP](#dump-to-zip-and-restore), and also restore
* Doc JSON tree editor, understands JS syntax
* Full text search in view results and JSON trees
* Group operations with DBs, ACLs, docs and sync tasks
* Multiple DB backup to external Couch, prefixed and filtered
* Node and cluster level config and stats monitoring
* View editor with JS, Erlang and Mango support
* Document revisions structured diff
* Local docs list and view
* Docs purge

Photon installation process is one-step: put `_design/photon` JSON doc into CouchDB. There are 3 ways: [command line](#installation-using-curl), [copy/paste](#installation-using-copypaste) and [replication](#installation-using-replication).

## Installation using curl

First, copy below script into text editor and provide admin username/password at the first line. Then copy/paste result to command line 
and press Enter. The script creates `photon` bucket, makes the bucket public (important step if you have CouchDB 3+), downloads Photon ddoc, and puts it into Couch.

```bash
uname=______; upwd=______; \
couch="-H Content-Type:application/json -X PUT http://$uname:$upwd@127.0.0.1:5984/photon"; \
curl $couch; curl https://raw.githubusercontent.com/ermouth/couch-photon/master/photon.json | \
curl $couch/_design/photon -d @- ; curl $couch/_security -d '{}' ; \
couch=''; uname=''; upwd=''
```

After process finished, open `http://127.0.0.1:5984/photon/_design/photon/index.html` in browser. 

Next time you can upgrade Photon directly from Photon itself: just click the rightmost button of navbar, then click `Check for updates` button.

## Installation using copy/paste
Download `photon.json` from [Github](https://raw.githubusercontent.com/ermouth/couch-photon/master/photon.json) or [AWS S3 CDN](https://s3-eu-west-1.amazonaws.com/cdn.cloudwall.me/photon/photon.json) and use one of the following ways:

a) Open JSON in any text editor. Create a doc in any CouchDB bucket, using Futon or Fauxton. Copy-paste JSON text into it. Save. Run `index.html` attachment, clicking it in Futon or Fauxton, or directly typing smth like `yourcouchdomain.xyz/photon/_design/photon/index.html`.

b) `curl -H Content-Type:application/json -X PUT http://yourdomain.com:5984/photon/_design/photon -d @photon.json`. Run `index.html`.

Next time you can upgrade Photon directly from Photon itself: just click the rightmost button of navbar, then click `Check for updates` button.

Please note: for CouchDB 3.x you should explicitly make `photon` DB public, otherwise you can’t start Photon not being logged in.

## Installation using replication
You can install Photon using native CouchDB replication. Since DB you will replicate from is of very limited capacity, please only replicate once, do not make sync continuous.

__For CouchDB 1.7.2 and earlier.__ Create new doc in your `_replicator` DB and copy-paste below JSON into it. Save – and you are done.
```json
{
  "_id":      "Photon",
  "source":   "https://photon.cloudwall.me/dist-photon",
  "target":   "photon",
  "create_target": true,
  "doc_ids":  ["_design/photon"],
  "user_ctx": {"name":"admin", "roles":["_admin"]}
}
```
To make sure `user_ctx` section works properly, you must have CouchDB proxy auth turned on. By default it’s active in CouchDB 1.x.

__For CouchDB 2+__ JSON is bit different (see below). You need to insert credentials since CouchDB 2+ does not understand `user_ctx` param.
```json
{
  "_id":      "Photon",
  "source":   "https://photon.cloudwall.me/dist-photon",
  "target":   "http://admin:__________@localhost:5984/photon",
  "create_target": true,
  "doc_ids":  ["_design/photon"]
}
```

Next time you can upgrade Photon directly from Photon itself, without repliction. Just click the rightmost button of navbar, then click `Check for updates` button.

Please note: for CouchDB 3.x you should explicitly make `photon` DB public, otherwise you can’t start Photon not being logged in.

## Configuring access

By default, Photon only starts if a user has `_admin` or `app-photon` role. Allowed roles are listed in `.settings.roles` branch of the Photon design document. You can edit this branch, save ddoc and reload Photon. 

Modified settings are preserved during Photon update if it’s performed using `Check for updates` button.

## Dump to ZIP and restore

The `Sync/dump…` dialog has special switch `ZIP` to manage DB dump/restore process. Photon can dump several DBs into one archive file, and later restore them, all or partially, under original or different names. In most browsers in safe environment (https) Photon can handle gigabytes of data without stalling.

Unlike all other Photon components ZIP processor relies on very modern browser technologies, which means ZIP features don’t work in browsers older than \~2017. 

ZIP processor tries to use streams, and also to employ almost all CPU power available, so given a DB of large docs it easily saturates 50Mbit/s network on very average notebook. 

Streaming doesn’t work in Safari <14.1, also no streaming in unsafe environment. No streaming means entire dump should fit in RAM, which still allows dump size up to several hundreds of megabytes.

All docs in a Photon-generated ZIP archive are named like `dbname/doc_id.json`. Also ZIP always contains `_info.json` file at the very start. This file contains DB list, security objects, stats, etc. Unzip can only work with archives having info file in the first megabyte of data. 

## CouchDB performance test

Photon design document includes superficial CouchDB performance test accessible from About tab for users with `_admin` role.

The test provides good insights how `q,n` cluster params impact performance. Also test shows JS query server costs, and how QS performance depends on sharding options.

## Dedicated host

Photon can run as a couchapp on a dedicated domain. To set up Photon for a dedicated host  
just configure CouchDB properly. First, [set up CORS](https://cloudwall.me/setup_couch#h-16ylld74) 
for the domain. Then set up two config keys:
```
[vhosts] 
photon.mydomain.xyz = /photon/_design/photon/_rewrite
[httpd]
secure_rewrites = false
```
Now typing `photon.mydomain.xyz` in browser runs Photon.

## FAQ

__Where are source files?__

Photon never existed as source _files_, its sources are CouchDB _docs_. Photon is developed and built using specialized dev environment, CloudWall. You can explore [Photon source code](https://cloudwall.me/_demo/#cw/Manifest/!WyJlZGl0IiwiY3ctUGhvdG9uLTFjY2QiXQ--) right in browser, using CloudWall demo built-in IDE.

__What is underlying technology?__

Photon employs most conservative and bullet-proof approaches whenever possible: ES5 sources, jQuery plus established plugins to render UI, XMLHttpRequest to interact with CouchDB, no corporate OSS libs. The only exception is ZIP processor, it requires modern browser technologies to 
work with decent speed, or to work at all.

__Is it safe to update from CDN?__

Default CDN has logging turned off, so requests and IPs are not collected. Update is always explicit and never performed until you click the update button. 

---

(c) 2021 ermouth. Photon is MIT licensed. Photon works in all modern browsers, IE11, and on iPad. 
