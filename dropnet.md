---
title: DropNet
layout: layout
---

<p>DropNet is a .NET client library for the Dropbox API.</p>

<h3>Where to get it?</h3>

<ul>
	<li>NuGet: <strong>PM&gt; Install-Package DropNet</strong> (<a href="http://nuget.org/List/Packages/DropNet" target="_blank">NuGet Gallery</a>) </li>
	<li>Download the source from github: <a href="https://github.com/dkarzon/DropNet" target="_blank">dkarzon/DropNet</a> </li>
</ul>

<h3>Whats supported?</h3>

<ul>
	<li>.NET 3.5 </li>
	<li>Windows Phone 7.1 (Mango) </li>
</ul>

<p>&#160;</p>

<h3>How do I use it?</h3>

<h4>The Client:</h4>

<p>To use DropNet you need an instance of the DropNetClient class, this class does everything for DropNet. This class takes the API Key and API Secret (These must be obtained from <a href="https://www.dropbox.com/">Dropbox</a> to access the API).</p>

{% highlight csharp %}_client = new DropNetClient("API KEY", "API SECRET");{% endhighlight %}

<p>&#160;</p>

<h4>Login/Tokens:</h4>

<p>Dropbox now requires a web authentication to get a usable token/secret, so this is a 3 step process.</p>

<p><strong>1. Get Request Token </strong>– This step gets an oauth token from dropbox (<strong>NOTE:</strong> the token must pass the other steps before it can be used)</p>

{% highlight csharp %}// Sync
_client.GetToken();

// Async
_client.GetTokenAsync((userLogin) =>
    {
        //Dont really need to do anything with userLogin, DropNet takes care of it for now
    },
    (error) =>
    {
        //Handle error
    });{% endhighlight %}

<p><strong>2. Authorize App with Dropbox </strong>– This step involves sending the user to a login page on the dropbox site and having them authenticate there.
The DropNet client has a function to return the url for you but the rest must be handled in app, this function also takes a callback url for redirecting the user to after they have logged in. (<strong>NOTE: </strong>The token still cant be used yet.)</p>

{% highlight csharp %}var url = _client.BuildAuthorizeUrl();
//Use the url in a browser so the user can login{% endhighlight %}

<p><strong>2.5 Open a browser with the url returned by BuildAuthorizeUrl </strong>– After we have the authorize url we need to direct the user there (use some sort of browser here depending on the platform) and navigate the user to the url. This will prompt them to login and authorize your app with the API.</p>

<p><strong>3. Get an Access Token from the Request Token</strong> – This is the last stage of the process, converting the oauth request token into a usable dropbox API token. This function will use the clients stored Request Token but this can be overloaded if you need to specify a token to use.</p>

{% highlight csharp %}// Sync 
var accessToken = _client.GetAccessToken(); //Store this token for "remember me" function

// Async
_client.GetAccessTokenAsync((accessToken) =>
    {
        //Store this token for "remember me" function
    },
    (error) =>
    {
        //Handle error
    });{% endhighlight %}

<p>&#160;</p>

<p><em>Best Practices:</em> <a href="https://www.dropbox.com/developers/docs">Dropbox’s Developer page</a> states several times in <strong><font color="#ff0000">bold red</font></strong> font that applications should not store a users Dropbox password and to help enforce this DropNet allows you to manually set a users Token and Secret on the client.</p>

{% highlight csharp %}_client = new DropNetClient("API KEY", "API SECRET", "USER TOKEN", "USER SECRET");
// OR
_client = new DropNetClient("API KEY", "API SECRET");
_client.UserLogin = new UserLogin { Token = "USER TOKEN", Secret = "USER SECRET" };{% endhighlight %}

<p>&#160;</p>

<h4>Get MetaData:</h4>

<p>The Dropbox API uses Metadata to navigate through the its folder structure. Metadata gives us access to file and folder details (such as modified dates, file size, folder contents, etc.)</p>

{% highlight csharp %}// Sync
var metaData = _client.GetMetaData("/Public"); //Folder

// Async
_client.GetMetaDataAsync("/Public",
    (metaData) =>
    {
        //Do something with MetaData
    },
    (error) =>
    {
        //Do something on error
    });{% endhighlight %}

<p>&#160;</p>

<h4>Get File:</h4>

<p>The Get File is where files can be downloaded from Dropbox, just give DropNet a path and it will download your file.</p>

{% highlight csharp %}// Sync
var fileBytes = _client.GetFile("/Getting Started.rtf");

// Async
_client.GetFileAsync("/Getting Started.rtf",
    (response) =>
    {
        //Do something with response
    },
    (error) =>
    {
        //Do something on error
    });{% endhighlight %}

<p>&#160;</p>

<h4>Upload File:</h4>

<p>DropNet has a few variations of the Upload File function, some taking a FileInfo object, a file path or a raw byte array.</p>

{% highlight csharp %}// Sync
var uploaded = _client.UploadFile("/", "test.txt", content); //FileInfo

// Async
_client.UploadFileAsync("/", "test.txt", content,
    (response) =>
    {
        //Do something with response
    },
    (error) =>
    {
        //Do something on error
    });{% endhighlight %}

<p>&#160;</p>

<h4>Copy/Move:</h4>

<p>Copy and Move functions are simple, give DropNet a source path and a destination path and it will move the file or folder from the source path to the destination path.</p>

{% highlight csharp %}// Sync
_client.Move("/Test.txt", "/Public/Test.txt");

// Async
_client.MoveAsync("/Test.txt", "/Public/Test.txt",
    (response) =>
    {
        //Do something
    },
    (error) =>
    {
        //Do something on error
    });{% endhighlight %}

<p>&#160;</p>

<h4>Delete:</h4>

<p>Delete deletes a a specified file or folder from Dropbox. (Its OK, they have a backup…)</p>

{% highlight csharp %}// Sync
_client.Delete("/Test.txt");

// Async
_client.DeleteAsync("/Test.txt",
    (response) =>
    {
        //Do something
    },
    (error) =>
    {
        //Do something on error
    });{% endhighlight %}

<p>&#160;</p>

<h4>Account Info:</h4>

<p>Gets your account info from Dropbox (mainly used for Quota information)</p>

{% highlight csharp %}// Sync
var accountInfo = _client.Account_Info();

// Async
_client.Account_InfoAsync((accountInfo) =>
    {
        //Do something with accountInfo
    },
    (error) =>
    {
        //Do something on error
    });{% endhighlight %}

<p>&#160;</p>

<h4>Create Folder:</h4>

<p>This creates a new folder on Dropbox at the specified path</p>

{% highlight csharp %}// Sync
var metaData = _client.CreateFolder("NewFolder1");

// Async
_client.CreateFolderAsync("NewFolder1",
    (metaData) =>
    {
        //Do something with metaData
    },
    (error) =>
    {
        //Do something on error
    });{% endhighlight %}

<h4>Share:</h4>

<p>This creates a temporary public link to a file.</p>

{% highlight csharp %}// Sync
var shareResponse = _client.GetShare("/Getting Started.rtf");

// Async
_client.GetShareAsync("/Getting Started.rtf",
    (shareResponse) =>
    {
        //Do something with shareResponse
    },
    (error) =>
    {
        //Do something on error
    });{% endhighlight %}

<h3>Thumbnails:</h3>

<p>Gets an image thumbnail for a file in Dropbox. (Can set thumbnail size, defaults to small, 32px)</p>

{% highlight csharp %}//Sync
var rawBytes = _client.GetThumbnail("/Temp/Test.png");

//Async
_client.GetThumbnailAsync("/Temp/Test.png",
    (rawBytes) =>
    {
        //Do something with rawBytes
    },
    (error) =>
    {
        //Do something on error
    });{% endhighlight %}

<h3>Media:</h3>

<p>Gets a media link for a media file in Dropbox. (used for streaming)</p>

{% highlight csharp %}//Sync
var medialink = _client.GetMedia("/Temp/AwesomeVideo1.mp4");

//Async
_client.GetMediaAsync("/Temp/AwesomeVideo1.mp4",
    (medialink) =>
    {
        //Do something with medialink
    },
    (error) =>
    {
        //Do something on error
    });{% endhighlight %}

<p>&#160;</p>

<p>Made something with DropNet? Tell me about it and I’ll list it here. (<a href="http://twitter.com/dkarzon">@dkarzon</a>)</p>

<p>If you have any questions about DropNet post a question on Stack Overflow - <a href="http://stackoverflow.com/questions/tagged/dropnet">http://stackoverflow.com/questions/tagged/dropnet</a></p>