---
title: DropNetRT
layout: layout
---

<p>
	DropNetRT is a Portable Class Library for .Net (including Windows Store and Windows Phone) applications to connect to the Dropbox API.
</p>

<div class="nuget-badge">
    <p>
		<code>PM&gt; Install-Package DropNetRT</code>
	</p>
</div>

<h3>Getting started</h3>
<p>
	To create an app that uses Dropbox you first need to register your app with Dropbox (<a href="https://dropbox.com/developers">Developer site</a>).
	This will give you the API Key and Secret combination required to use the API.
</p>
<p>
	Once you have your API Key and Secret download DropNetRT from NuGet (or alternatively download the source from GitHub).
</p>

<h3>DropNetClient</h3>
<p>
	To start using DropNetRT create an instance of the DropNetClient class using your API Key and Secret generated from the Dropbox developer site.
</p>
{% highlight csharp %}var client = new DropNetClient("API KEY", "API SECRET");{% endhighlight %}
<p>
	The constructor has an overload method for if you have already authenticated with the API and have an <em>access token</em> for the user. (covered in Authentication).
</p>
{% highlight csharp %}var client = new DropNetClient("API KEY", "API SECRET", "USER TOKEN", "USER SECRET");{% endhighlight %}

<h3>Authentication</h3>
<p>
	The dropbox API uses oauth for authenticating, it does both v1 and v2 but at the moment DropNetRT only supports oauth v1.
</p>
<p>
	Authentication is a 3 step process.
</p>
<ul>
	<li>Step 1: Get a <em>request token</em> from the API.</li>
	<li>Step 2: Navigate the user to the dropbox site to login (using the <em>request token</em>).</li>
	<li>Step 3: Convert the <em>request token</em> into an <em>access token</em> to use the rest of the API.</li>
</ul>
<br />

<p>
	<strong>Step 1:</strong> Get a request token from the API. Call the GetRequestToken function to create a new request token.
	<em>Note: The DropNetClient instance will save this as the UserLogin property but is also returned by the function.</em>
</p>
{% highlight csharp %}var requestToken = await client.GetRequestToken();{% endhighlight %}
<p>
	<strong>Step 2:</strong> Navigate the user to the dropbox site to login (using the request token).
</p>
{% highlight csharp %}var url = client.BuildAuthorizeUrl(requestToken, "http://dkdevelopment.net");{% endhighlight %}
<p>
	From here you will need to open a browser with the url given to allow the user to login. Then wait for the callback the the given callback url before moving on.
	An example of this step can be found in  the WP8 sample app (<a href="https://github.com/dkarzon/DropNetRT/blob/master/DropNetRT.Sample.WP8/MainPage.xaml.cs#L58">source on github</a>).
	WinRT apps can use the WebAuthenticationBroker for this, just give it the url and callback parameters then parse the response for the token querystring parameter. (Note the secret doesn't change, only the token)
</p>
<p>
	<strong>Step 3:</strong> Convert the request token into an access token to use the rest of the API. To do this call the GetAccessToken function.
	<em>Note: The DropNetClient instance will save this as the UserLogin property but is also returned by the function. Save this token so the user doesn't have to reauthenticate each time.</em>
</p>
{% highlight csharp %}var accessToken = await client.GetAccessToken();{% endhighlight %}

<h3>App folder/Sandbox mode</h3>
<p>
	To set the DropNetClient to use an app folder instead of the users full dropbox folder set the UseSandbox property to true. This may cause the client to throw exceptions if the app is set to only be allowed access to app folders.
</p>
{% highlight csharp %}client.UseSandbox = true;{% endhighlight %}

<h3>User functions</h3>
<p><em>coming soon</em></p>

<h3>File functions</h3>
<p>
	<strong>Copy</strong><br />
	Copies a file or folder from one location to another.
</p>
{% highlight csharp %}var copiedMetadata = await client.Copy("/from-path/file", "/to-path");{% endhighlight %}

<p>
	<strong>Create Folder</strong><br />
	Creates an empty folder with the given path
</p>
{% highlight csharp %}var folderMetadata = await client.CreateFolder("/new-folder-to-create");{% endhighlight %}

<p>
	<strong>Delete</strong><br />
	Deletes a file or folder given its path
</p>
{% highlight csharp %}var deletedMetadata = await client.Delete("/file-to-delete");{% endhighlight %}

<p>
	<strong>Get Delta</strong><br />
	Gets the Delta (changes) of a given path and cursor from the last time it was called (otherwise returns last 2000 folder changes)
</p>
{% highlight csharp %}var deltaPage = await client.GetDelta("cursor");{% endhighlight %}

<p>
	<strong>Get File</strong><br />
	Gets a file from dropbox given the path
</p>
{% highlight csharp %}var fileBytes = await client.GetFile("/file-to-get");{% endhighlight %}

<p>
	<strong>GetMetaData</strong><br />
	Gets info on a file or folder (including folder contents). There are a few functions for getting an items Metadata, most of them are shorthand methods of the main one: <em>GetMetaData(string path, string hash, int? rev, bool list, bool includeDeleted)</em>
</p>
{% highlight csharp %}var metadata = await client.GetMetaData("/test");{% endhighlight %}

<p>
	<strong>GetShare</strong><br />
	Gets a share link for a file or folder.
</p>
{% highlight csharp %}var shareLink = await client.GetShare("/test");
var nonShortShareLink = await client.GetShare("/test", false);{% endhighlight %}

<p>
	<strong>Search</strong><br />
	Searches for a given string.
</p>
{% highlight csharp %}var resultsList = await client.Search("test");
var resultsList = await client.Search("test", "/path-to-search");{% endhighlight %}

<h3>Platform support</h3>
<p>DropNetRT is a PCL (Portable Class Library) which means the same dll can be used across multiple platforms. Here are the target frameworks:</p>
<ul>
	<li>.NET 4.5</li>
	<li>.NET for Windows Store apps (WinRT)</li>
	<li>Windows Phone 7.5 and higher</li>
	<li>Silverlight 4 and higher</li>
</ul>

<h3>Samples</h3>
<p><em>coming soon</em></p>

<p>If you have any questions about DropNet post a question on Stack Overflow - <a href="http://stackoverflow.com/questions/tagged/dropnet">http://stackoverflow.com/questions/tagged/dropnet</a></p>