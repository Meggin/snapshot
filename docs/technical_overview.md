# Snapshot Technical Overview

Briefly,
Snapshot is a web application that lets users capture, upload, and edit images.
Image editing options include saturation, warmth, brightness, blur, contrast, vignette, and gray.

This overview describes the main components of the app,
steps to add a new view, and a high level description of it's PWA features.

## Main components

### Base view

### Index

### Router

### Image shader

## Steps to add a new view

* This is so you have a view to point to once you are doing all the other stuff, but it won’t do anything yet.

### First import base view class:

<span class="c6 c18">

<span class="c6">import<span class="c7"> View <span class="c6">from<span class="c7"> <span class="c19">'./view'<span class="c7 c18">;

<span class="c7 c18">

### Then create new upload class that extends it (and don’t forget to export it):
    

<span class="c6">export<span class="c7"> <span class="c6">default<span class="c7"> <span class="c6">class<span class="c7"> <span class="c24">UploadView<span class="c7"> <span class="c6">extends<span class="c7"> <span class="c24">View<span class="c7 c18"> {

<span class="c7 c18">}

<span class="c7 c18">

### Pass in the super constructor:

<span class="c6">export<span class="c7"> <span class="c6">default<span class="c7"> <span class="c6">class<span class="c7"> <span class="c24">UploadView<span class="c7"> <span class="c6">extends<span class="c7"> <span class="c24">View<span class="c7 c18"> {

<span class="c7 c18">                 constructor() {
                          super(document.getElementById('upload-view')!);
                 }

<span class="c7">}

### <span class="c14">Create Upload route in router

1.  In <span class="c5">[router.ts](https://www.google.com/url?q=https://github.com/GoogleChrome/snapshot/blob/ab53d2495706875ecc1740384f8562c8c7eaa455/src/router.ts&sa=D&ust=1501791703222000&usg=AFQjCNGzHeFCac0Yjw1Q6038OgCA8uYioA), add import for upload view:


        <span class="c6">import<span class="c7"> UploadView <span class="c6">from<span class="c7"> <span class="c19">'./upload-view'<span class="c7 c18">;

<span class="c7 c18">

1.  Add private property in Router class:

        

        <span class="c6">private<span class="c7"> uploadView<span class="c6">:<span class="c7"> <span class="c24">UploadView<span class="c7">;

        

1.  Create upload view object in Router constructor:



<span class="c29">this<span class="c7">.uploadView <span class="c6">=<span class="c7"> <span class="c6">new<span class="c7"> <span class="c24">UploadView<span class="c7">();



1.  Add changeHandler case for upload view:

        

             case 'upload':
                       newView = this.uploadView;
                       break;



At this point, check that you can see the /upload page in the local browser.



IMPORTANT: need to clear the cache to be able to see the page (due to service worker). In the console.log, run caches.delete(‘snapshot’) and this makes it happen without having to manually clear the cache in the DevTools UI. Cool tip. (I’ve been clearing manually.) (<span class="c5">[Link to service worker file for snapshot.](https://www.google.com/url?q=https://github.com/GoogleChrome/snapshot/blob/master/public/sw.js&sa=D&ust=1501791703225000&usg=AFQjCNEZi3ndvsU4kZEM7FmiEnggmR1ifA))



Mat talks a bit about the service worker file-- basically we are trying to get the service wroker to get index.html from the network and not fallback to the cached version. Weirdly enough though, index.html doesn’t appear in the list of network resources, when cache is deleted, but it still must be getting from the network as we deleted the cache, and the new upload page is showing in the local server. Service worker stuff is tricky. I want to pay attention to some of these narratives, as we might provide some cool tips throughout the docs on handling the testing/iterating of things with a live sw.

## <span class="c18 c22 c15">Update index.html

### <span class="c14">Create new toolbar

* This is based on reviewing the code, and appears to be the refactored way of handling the upload and capture buttons on the browse view page.

Tool bar includes both the capture and upload buttons.

### <span class="c14">Add Upload button to toolbar

Before even being able to do this, Mat located a button icon that would match upload in material.io/icons (it’s called file upload), he copied the svg code, and then he created a new button for it in index.html called svg-file-upload, that he could now reference throughout the app.



Here’s what the snippet looks like for the svg code in index.html:



<g id="svg-file-upload">
        <path d="M0 0h24v24H0z" fill="none"/>
        <path d="M9 16h6v-6h4l-7-7-7 7h4zm-4 2h14v2H5z"/>
</g>



And here’s HTML for new button that references the new button icon:



<button class="light" id="browse-upload-button" aria-label="Upload photo" tabindex="0">
        <svg viewBox="0 0 24 24">
                <use href="#svg-file-upload" />
        </svg>
</button>



At the moment, all html and css in this single file. Eventually that will change, but interesting to note that developers often start with one file and then break things out.



Also add this after capture (need to find out more exactly what this is):



IMPORTANT: later in Mat’s video, he explains a bit more about this part of the code in index.html. I want to call attention specifically to the input type file, which basically shows the choose file button on the upload page.



And a user can click it and select a file to upload with that one line of code in index.html; however, the file doesn’t actually show up in the upload view page. More has to be done to that page to show the file selected.



But once the user selects a file, nothing happens.



   <!-- Upload -->

   <div class="view-element hidden" id="upload-view">
     <div class="toolbar left">
        <button class="dark" id="upload-view-close" aria-label="Close" tabindex="0">
          <svg viewBox="0 0 24 24">
            <use href="#svg-close" />
          </svg>
        </button>
      </div>
      <div class="toolbar bottom">
        <input type="file" id="upload-file-input" accept="image/*">
      </div>
  </div>

### Style toolbar and buttons



Toolbar css (toolbar includes both upload and capture buttons):



      .toolbar.floating {
        position: absolute;
        margin: 5px;
        bottom: 0;
        right: 0;
     }



Upload and capture buttons, inactive styles:



      #browse-view button {
        width: 54px;
        height: 54px;
        background-color: #50E3C2;
     }



Upload and capture buttons, active/hover styles:



    #browse-view button:focus, #browse-view button:hover {
        background-color: white;
   }

## <span class="c18 c22 c15">Update Browse View

This is the page where you browse photos, and includes the links to either capture a new photo, or upload an existing photo.

### <span class="c14">Add upload button

First add button element:



<span class="c30">private<span class="c12"> uploadButton<span class="c30">:<span class="c12"> <span class="c26">HTMLButtonElement<span class="c12 c18">;

<span class="c12 c18">

Then link button element to element ID in index.html:



<span class="c17">this<span class="c12">.uploadButton <span class="c30">=<span class="c12"> <span class="c17">document<span class="c12">.<span class="c17">getElementById<span class="c12">(<span class="c8">'browse-upload-button'<span class="c12">) <span class="c30">as<span class="c12"> <span class="c26">HTMLButtonElement<span class="c12 c18">;

<span class="c12 c18">

Bind button element to listener:



<span class="c17">this<span class="c12">.uploadButton.<span class="c17">addEventListener<span class="c12">(<span class="c8">'click'<span class="c12">, () <span class="c30">=><span class="c12"> <span class="c17">this<span class="c12">.<span class="c26">uploadClick<span class="c12 c18">());

Handle button click, by routing to upload page:



uploadClick() {
   router.visit('/upload');
}
