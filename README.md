# couch-photon
CouchDB admin tool, modernized Futon. Beta, but covers 100% of Futon features. See screencast at https://youtu.be/MHc6tozNhWU

## Install
Download `photon.json`, and use one of the following ways:

a) Open JSON in any text editor. Create a doc in any CouchDB bucket, copy-paste JSON text into it. Save. Run `index.html`.

b) `curl -H 'Content-Type: application/json' -X PUT http://yourdomain.com:5984/somedb/_design/photon -d @photon.json`. Run `index.html`.

Next time you can upgrade Photon directly from Photon, clicking rightmost button on navbar and checking update directly from CDN.
