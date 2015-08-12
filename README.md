## Katappa

A block based Rich Text editor.

It uses:
* [ReactJS](http://facebook.github.io/react/) for its UI.
* [Quill](http://quilljs.com) for rich text editing support.
* [fetch](https://github.com/github/fetch) for ajax calls to server (used when image files are required to be uploaded).

### Installation
* Use `bower install katappa`.
* Or Download the latest release [here](https://github.com/brijeshb42/katappa/releases/latest).

### Usage
It is available in the `window` as `Katappa`.

#### Load dependencies in your html:
* CSS:
    * `<link rel="stylesheet" type="text/css" href="bower_components/quill/dist/quill.base.css">`
    * (A bundled quill css comes with `Katappa`. If you don't use bower, you can directly use:
        * `<link rel="stylesheet" type="text/css" href="katappa.vendor.css">`)
    * `<link rel="stylesheet" type="text/css" href="katappa.css">`. (Used for basic styling of the blocks. You can extend these to be what you want.)
* Javascript:
    * `<script type="text/javascript" src="bower_components/react/react-with-addons.min.js"></script>`
    * `<script type="text/javascript" src="bower_components/quill/dist/quill.min.js"></script>`
    * `<script src="bower_components/fetch/fetch.js"></script>`
    * (Or you can just include `<script src="katappa.vendor.js"></script>` instead of the above 3).
    * `<script type="text/javascript" src="katappa.js"></script>`

* Html:
    * `<div id="editor-ui"></div>`

* Script:
    * First create an `editor` instance.
    * Then mount it to the desired DOM node.

```javascript
var editor = React.createElement(Katappa.Editor);
React.render(editor, document.getElementById('editor-ui'));
```

The editor has a `Save` button when there are 1 or more blocks. To get the current block content, you can pass a callback function `onSave` as a React `prop` that will be called whenever `Save` button is clicked. This callback receives the current `blocks` array.

```javascript
var editor = React.createElement(Katappa.Editor, {
    onSave: function(blocks) {
        console.log('This is the list of current blocks.');
        console.log(blocks);
    }
});
React.render(editor, document.getElementById('editor-ui'));
```

If you already have a list of blocks (that may have been previously saved on the server):
    * You can first fetch the block list from the server.
    * Pass the list to the editor instance as a React `prop`.
    * Make sure each block has a `key` key. This is used by React and facilitates easy manipulation of position (up, down or remove block).
    * If the blocks don't have a `key`, you can just generate keys for each of them in the browser using the utility function provided `Katappa.uuid()`.
    * The `key` functionality applies to each of the items in `UL` or `OL`.

```javascript
var blocks = function() {
    var blocks = [];
    // fetch from server, and assign it to blocks.
    for(var i=0; i < blocks.length; i++) {
        if(!blocks[i].key) {
            blocks[i].key = Katappa.uuid();
        }
        if(blocks[i].type === Katappa.Blocks.UL.Name || blocks[i].type === Katappa.Blocks.OL.Name) {
            for(var j = 0; j < blocks[i].content.length; j++) {
                if(!blocks[i].content[j].key) {
                    blocks[i].content[j].key = Katappa.uuid();
                }
            }
        }
    }
    return blocks;
};
var editor = React.createElement(Katappa.Editor, {
    blocks: blocks,
    onSave: function(blocks) {
        console.log('This is the list of current blocks.');
        console.log(blocks);
    }
});
React.render(editor, document.getElementById('editor-ui'));
```

#### Image upload
* By default, the image block just renders the image using `createObjectURL`.
* If you want the image to also be uploaded to the server, you can do this:
    * Change the value of `UploadUrl` to your server's endpoint
        * `Katappa.Block.Image.UploadUrl = '/upload_image`
    * If `UploadUrl` is provided, the `Image` block will send a POST request to the url with `image` key having the selected image file and it expects a `json` reponse from the server of the following format:
```json
{
    "type": "success",
    "message": "http://absoluteurl.to/the/uploaded/image.jpg"
}
```
#### Goodies
* `Katappa` supports the following embeds out of the box:
    * Instagram
    * Youtube
    * Vimeo
* Also has Twitter embed in `katappa.embed.js`. But this also has a dependency on server.
* The Twitter embed expects:
    * The server to implement a `/tweet` endpoint that will be called with the `url=url_to_twitter_status` query string.
    * The response from the server should be a json with the following structure:
    ```json
    {
        "html": "The html retrived when visiting 'https://api.twitter.com/1/statuses/oembed.json?url=url_to_twitter_status' directly"
    }
    ```

* Current Blocks:
    - [x] Text
    - [x] Quote
    - [x] Image
    - [x] Horizontal Break
    - [x] Ordered List
    - [x] Unordered List
    - [x] Embeds
        - [x] Twitter (has dependency on server)
        - [x] Instagram
        - [x] Vimeo
        - [x] Youtube

* Extra features:
    * Blocks can be rearranged.
    * Existing blocks can be deleted.
    * Automatic image upload if UploadUrl is provided.

#### Todo
* Add instructions to create custom blocks.

#### Known issues
* Better handling of `Return` key press when inside one of the `Text` blocks. (Text, Quote, H2, UL, OL)
* If a Twitter embed block is rearranged to make it go upwards, the rendered part disappears from the UI.

### Made while working @ [http://scroll.in](http://scroll.in)