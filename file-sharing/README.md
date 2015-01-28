# VideoLinkPlatform Example - File Sharing

With VideoLinkPlatform SDK is easy to share files in a peer-to-peer connection and
this example shows you how.

First make sure to have VideoLinkPlatform JS SDK
```html
<script src="//cdn.videolinkplatform.com/vl_api.js"></script>
```

Then you need to initialize the vlp Connector object with your API key and
the desired connection type, you can read more about VideolinkPlatformConnector
in the [documentation](http://videolinkplatform.com/apidocs.html#VideolinkPlatformConnector)
```js
var vlpConnector = new VideolinkPlatformConnector({
  apiKey: '<your API key>',
  connectionType: 'chat-only'
}, function success(connector){...}, function failure(error){...})
```

VideolinkPlatformConnector constructor takes a success callback as argument
which give us access to the initialized connector, you can use it to configure
the room.
```js
var roomName = 'file-share';
connector.createRoom({name: roomName}, function(room){
  vlpRoom = room;
  ...
```

Every room has its own FileSharingService, you need to retrieve it in order to
configure our filehandling events.
```js
  var fileSharingService = vlpRoom.getFileSharingService();
```

There are plenty number of events in the file sharing process (as you can see
[here](http://videolinkplatform.com/file-sharing.html#Events)), but for this
example we just need to handle `onFileRequest` and `onDownloadCompleted`.
```js  
  fileSharingService.onFileRequest(function(fileTransferId, name) {
    if (confirm('Would you like to download file: ' + name)) {
      fileSharingService.acceptFileSharingRequest(fileTransferId);
    }
  });

  fileSharingService.onDownloadCompleted(function(fileId, name, fileData, type, mime) {
    addDownloadedFile(fileId, name, fileData, type, mime);
  });
```

Now we have configured the room and the file sharing events, but we can't send
files yet, that's why we need `fileSharingService.startFileSharing(file)` as you
can see here.
```js
function sendP2PFile (file) {
  if (!!vlpRoom){
    var fileSharingService = vlpRoom.getFileSharingService();
    fileSharingService.startFileSharing(file);
  }
}
```

And that's it, we have our file sharing js app.
