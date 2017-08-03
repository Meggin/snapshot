# About Upload View

[In this video](https://www.google.com/url?q=https://drive.google.com/open?id%3D0ByKjxrGjtglDbkUxQUZuTTgtX1E&sa=D&ust=1501791703217000&usg=AFQjCNGbTDszbJcpUBSEZtzbZjEi3S0eiw),
Mat Scales implements live a new upload feature.
This is a doc describing his coding adventure,
organized in a way that heads in the direction of possible doc outputs.

## <span class="c18 c22 c15">Add upload functionality</span>

<span class="c2">Upload view page exists, but it doesn’t do anything yet. This is all about making it possible to upload files. For sure, it’s worth re-iterating the input type file upload in index.html that we covered already in the docs.</span>

<span class="c2"></span>

<span class="c2">So basically, the user has selected a file with Choose File input - but the file doesn’t actually show up. This is about adding the code to make the file show up, so that the user can confirm that they want to edit it, and presumably we want to route to the edit page with the uploaded file.</span>

<span class="c2"></span>

<span>Mat actually finds a cool doc on MDN that shows a good bit of what he is trying to figure out, including drag and drop:</span> <span class="c5">[https://developer.mozilla.org/en-US/docs/Using_files_from_web_applications](https://www.google.com/url?q=https://developer.mozilla.org/en-US/docs/Using_files_from_web_applications&sa=D&ust=1501791703233000&usg=AFQjCNHe7LFddwZ_pSsd2QV_b_UoaYtLgQ)</span><span class="c2"> </span>

### <span class="c14">Get a reference to button and set a listener to it</span>

<span class="c2">Also adding reference to close button and listener for close button.</span>

<span class="c2"></span>

<span class="c2">All going within the constructor on the upload view page:</span>

<span class="c2"></span>

<span class="c2"> constructor() {
   super(document.getElementById('upload-view')!);

   this.uploadInput = document.getElementById('upload-file-input')! as HTMLInputElement;
   this.closeButton = document.getElementById('upload-view-close')! as HTMLButtonElement;

   this.uploadInput.addEventListener('change', () => this.inputChange());
   this.closeButton.addEventListener('click', () => this.close());
 }</span>

<span class="c2"></span>

<span class="c11">Interesting note to point out:</span>

<span class="c2">Mat talks about the ! added to the end of the uploadInput reference-- this is to insist that the value won’t be null, so trust it. Basically if there’s a null value, the app isn’t working-- and there’s no point in watching out at runtime if the app totally doesn’t work at this granular level-- testing if the app does or doesn’t work should be detected at this level. The exclamation mark says yes, definitely, the element exists. The same kind of idea applies to saying it will be uploaded as an HTMLInputElement.</span>

### <span class="c14">Handle input change</span>

<span class="c2">Write inputChange() to handle what comes next when a file is selected.</span>

<span class="c2"></span>

<span class="c2">Here things get very interesting. Mat does a simple console log returning the file selected object, and yes, you can get a bunch of information about the selected file.</span>

<span class="c2"></span>

<span class="c2">But what he really wants to do is get the file into a blob. So he goes in search for a better doc.</span>

<span class="c2"></span>

<span class="c2">It’s kind of cool, as his doc hunt helps him to realize that the file he’s selected is a blob with a bit more info. So he is safe to assume once he has the file (which the console log is successfully showing), he’s got the blob.</span>

<span class="c2"></span>

<span class="c2">Next step is to re-use similar code that is in capture code to store the blob.</span>

<span class="c2"></span>

<span class="c2">This first requires importing similar files needed:</span>

<span class="c2"></span>

<span>i</span><span class="c4">mport db from './image-db';</span>

<span class="c4">import ImageRecord from './image-record';</span>

<span class="c4">import router from './router';</span>

<span class="c4">import View from './view';</span>

<span class="c2"></span>

<span class="c2">And then writing the code:</span>

<span class="c2"></span>

<span class="c4">inputChange() {
   const files = this.uploadInput.files;
   if (!files) {
     return;
   }
   const file = files[0];
   const record = new ImageRecord(file);
   db.store(record).then((id) => router.visit(`/image/${id}`));
 }</span>
