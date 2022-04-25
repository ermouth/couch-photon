# <img align="right" src="http://jquerymy.com/kod/photon-github.png" /> couch-photon
Photon is an alternative CouchDB admin panel with all standard and a lot of unique features. Photon is a single CouchDB design document with attachments, so it can be installed on any running CouchDB without rebuilding or reconfiguring.

Basic Photon features are shown in a short screencast at [youtu.be/M9ptWXfwMN8](https://youtu.be/M9ptWXfwMN8).

Photon is completely self-contained and is safe for restricted networks. Unless configured [differently](#configuring-access), users must have `_admin` or `app-photon` role to use Photon; this restriction only protects the app, it doesn’t add more security to CouchDB itself.

## Unique features

* [Group dump to ZIP](#dump-to-zip-and-restore), and also restore
* Instant search in view results and JSON trees
* JSON editor with JS syntax and out-of-order undo
* Group operations with DBs, ACLs, docs and sync tasks
* Node and cluster level stats display and config
* View editor with JS, Erlang and Mango support
* Document revisions structured diff
* Local docs list and view
* Conflicts list
* Docs purge

## Installation

Photon installation process is one-step: put `_design/photon` JSON doc into CouchDB. There are 3 ways: [command line](#install-using-curl), [copy/paste](#install-using-copypaste) and [replication](#install-using-replication).

## Install using curl

First, copy the script below into a text editor and provide admin username/password in the first line. Then copy/paste result to the command line and press Enter. The script creates `photon` bucket, makes the bucket public (an important step if you have CouchDB 3+), downloads Photon ddoc, and puts it into Couch.

__For CouchDB 3.0 and earlier:__
```bash
uname=______; upwd=______; \
couch="-H Content-Type:application/json -X PUT http://$uname:$upwd@127.0.0.1:5984/photon"; \
curl $couch; curl https://raw.githubusercontent.com/ermouth/couch-photon/master/photon.json | \
curl $couch/_design/photon -d @- ; curl $couch/_security -d '{}' ; \
couch=''; uname=''; upwd=''
```

__For CouchDB 3.1+,__ Couch config tuning added.
```bash
uname=______; upwd=______; \
couch="-H Content-Type:application/json -X PUT http://$uname:$upwd@127.0.0.1:5984"; \
curl $couch/photon; curl https://raw.githubusercontent.com/ermouth/couch-photon/master/photon.json | \
curl $couch/photon/_design/photon -d @- ; curl $couch/photon/_security -d '{}' ; \
curl $couch/_node/_local/_config/csp/attachments_enable -d '"false"' ; \
curl $couch/_node/_local/_config/chttpd_auth/same_site -d '"lax"' ; \
couch=''; uname=''; upwd=''
```

After the process is finished, open `http://127.0.0.1:5984/photon/_design/photon/index.html` in browser. 

Next time you can upgrade Photon directly from Photon itself: just click the rightmost button on the navbar, then click `Check for updates` button.

## Install using copy/paste

Download `photon.json` from [Github](https://raw.githubusercontent.com/ermouth/couch-photon/master/photon.json) or [CloudWall CDN](https://cdn.cloudwall.me/photon/photon.json) and use one of the following ways:

a) Open JSON in any text editor. Create a doc in any CouchDB bucket, using Futon or Fauxton. Copy-paste JSON text into it. Save. Run `index.html` attachment, clicking it in Futon or Fauxton, or directly typing smth like `yourcouchdomain.xyz/photon/_design/photon/index.html`.

b) `curl -H Content-Type:application/json -X PUT http://yourdomain.com:5984/photon/_design/photon -d @photon.json`. Run `index.html`.

Next time you can upgrade Photon directly from Photon itself: just click the rightmost button on the navbar, then click `Check for updates` button.

**For CouchDB 3.x:** you should explicitly make `photon` DB public to run Photon. For 3.1+ you also need to set `csp/atatchments_enable` config key to `false`, and `chttpd_auth/same_site` to `lax`.

## Install using replication

You can install Photon using native CouchDB replication. Since the DB you will replicate from has very limited capacity, please only replicate once, do not make sync continuous.

__For CouchDB 1.7.2 and earlier.__ Create a new doc in your `_replicator` DB and copy-paste below JSON into it. Save – and you are done.
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

Next time you can upgrade Photon directly from inside Photon without replication. Just click the rightmost button on the navbar, then click `Check for updates`.

**For CouchDB 3.x:** you should explicitly make `photon` DB public to run Photon. For 3.1+ you also need to set `csp/atatchments_enable` config key to `false`, and `chttpd_auth/same_site` to `lax`.

## Configuring access

By default, Photon only starts if a user has `_admin` or `app-photon` role. Allowed roles are listed in `.settings.roles` branch of the Photon design document. You can edit this branch, save ddoc and reload Photon. 

To preserve settings during Photon update use `Check for updates` button, not replication.

## Dump to ZIP and restore

The `Sync/dump…` dialog has special switch `ZIP` to manage the DB dump/restore process. Photon can dump several DBs into one archive file, and later restore them, in full or in part, under the original or different names. In most modern browsers in safe environment (https) Photon can handle gigabytes of data without stalling.

ZIP processor tries to use streams, and also to employ almost all CPU power available, so given a DB of large docs it easily saturates 55Mbit/s wifi on a very average machine. 

## CouchDB performance test

Photon design document includes superficial CouchDB performance test accessible from About tab for users with `_admin` role.

The test provides good insights how `q,n` cluster params impact performance. Also, the test shows JS query server costs, and how QS performance depends on sharding options.

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
Now typing `photon.mydomain.xyz` in the browser runs Photon.

## FAQ

__Where are source files?__

Photon never existed as source _files_, its sources are CouchDB _docs_. Photon is developed and built using specialized dev environment, CloudWall. You can explore [Photon source code](https://cloudwall.me/_demo/#cw/Manifest/!WyJlZGl0IiwiY3ctUGhvdG9uLTFjY2QiXQ--) right in the browser, using CloudWall demo built-in IDE.

__What is underlying technology?__

Photon employs conservative and bullet-proof approaches whenever possible: mostly ES5 sources, jQuery plus established plugins to render UI, XMLHttpRequest to interact with CouchDB, no corporate OSS libs. The only exception is the ZIP processor, it requires modern browser technologies to work with decent speed, or to work at all.

__Is it safe to update Photon from the default CDN?__

Basically, CloudWall CDN is the “source of truth” for all other places where latest stable version of Photon is available. Default CDN has very superficial logging, however if you are concerned about your node IP is logged during update, you always can download Photon json from Github manually.

Note that updates are always explicit and are never performed until you click the update button. Time to time update reminder never makes any requests to check for new version, it’s just a timer.

---

(c) 2022 ermouth. Photon is MIT licensed. Works in all modern browsers. 
