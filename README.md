# couch-photon
CouchDB admin tool, modernized Futon. Early beta, but what is clickable essentially works. 

May hang IE Edge. Tested very superficially since work is in progress.

## Install
Download JSON, and use one of following ways:

a) Open JSON in editor. Create a doc in any CouchDB bucket, copy-paste JSON text into it. Save. Run `index.html`.

b) `curl -H 'Content-Type: application/json' -X PUT http://yourdomain.com:5984/somedb/_design/photon -d @photon-0.2.720.json`.

Next time you can upgrade Photon directly from Photon, copy-pasting updates to design doc.
