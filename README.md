![Icon](https://raw.github.com/SimonCropp/Chevron/master/Icons/package_icon.png)

Wraps [HandlebarsJS](http://handlebarsjs.com/) to make it usable from .net.

## Nuget

#### [Chevron.IE](http://nuget.org/packages/Chevron.IE) 

Has a dependency on the [MsieJavaScriptEngine](http://www.nuget.org/packages/MsieJavaScriptEngine) package. 

#### [Chevron.IE.Merged](http://nuget.org/packages/Chevron.IE.Merged) 

ILMerges the [MsieJavaScriptEngine](http://www.nuget.org/packages/MsieJavaScriptEngine) assembly to avoid an extra dependency 

#### [Chevron.V8](http://nuget.org/packages/Chevron.V8) 

Has a dependency on the [ClearScript.V8](http://www.nuget.org/packages/ClearScript.V8) package. 

#### [Nancy.ViewEngines.Handlebars.IE](http://nuget.org/packages/Nancy.ViewEngines.Handlebars.IE) 

Has a dependency on the [MsieJavaScriptEngine](http://www.nuget.org/packages/MsieJavaScriptEngine) and Chevron.IE packages. 

#### [Nancy.ViewEngines.Handlebars.IE.Merged](http://nuget.org/packages/Nancy.ViewEngines.Handlebars.IE.Merged) 

ILMerges the [MsieJavaScriptEngine](http://www.nuget.org/packages/MsieJavaScriptEngine) and Chevron.IE assembly to avoid an any extra dependencies. 

#### [Nancy.ViewEngines.Handlebars.V8](http://nuget.org/packages/Nancy.ViewEngines.Handlebars.V8) 

Has a dependency on the [ClearScript.V8](http://www.nuget.org/packages/ClearScript.V8) and Chevron.V8 packages. 

## What Nuget to choose

### IE or V8

#### Performance

In my basic testing V8 was (in most cases) faster than IE. Note that this testing was done on the IE 10 engine and the IE 11 engine is rumoured to have significant performance improvements.

#### Consistency

When using the V8 engine you can control the specific version that is used. When using the IE engine the specific version of the engine can vary with windows updates. 

#### Portability

V8 will, in theory, run on a non windows platform (I have not done any testing of this). IE is tied to Windows.

#### Packaging and deployment

V8 is a native dll, to the nuget package needs to tweak your project file to manually copy it tot he output directory. While this generally works it is is more moving pieces and not seamless when upgrading packages or when double hop references are used. IE is a single .net dll and is manages as such

#### Size

IE is significantly smaller than V8

### Merged IE or Non-Merged IE

If you only want to render simple files then use the merged version. If you want full control over the JS engine (eg to load custom scripts) then use the non-merged version.

## Usage

### Rendering a template

#### Input
```
var source = @"<p>Hello, my name is {{name}}. I am from {{hometown}}. I have ' +
        '{{kids.length}} kids:</p>' +
        '<ul>{{#kids}}<li>{{name}} is {{age}}</li>{{/kids}}</ul>";

var context = new
{
    name = "Alan",
    hometown = "Somewhere, TX",
    kids = new[]
        {
            new
            {
                name = "Sally",
                age = "4"
            }
        }
};

using (var handleBars = new Handlebars())
{
    handleBars.RegisterTemplate("myTemplate", source);
    Approvals.Verify(handleBars.Transform("myTemplate", context));
}
```

#### Output
```
<p>Hello, my name is Alan. I am from Somewhere, TX. I have 1 kids:</p>
<ul>
	<li>Sally is 4</li>
</ul>
```

### Register Helpers

#### Input
```
var source = "<ul>{{#posts}}<li>{{link_to}}</li>{{/posts}}</ul>";
var context = new
{
    posts = new[]
        {
            new
            {
                url = "/hello-world",
                body = "Hello World!"
            }
        }
};
using (var handleBars = new Handlebars())
{
    handleBars.RegisterHelper("link_to",
        @"function() {
return new Handlebars.SafeString(""<a href='"" + this.url + ""'>"" + this.body + ""</a>"");
}");
    handleBars.RegisterTemplate("myTemplate", source);
    Approvals.Verify(handleBars.Transform("myTemplate", context));
}
```
#### Output
```
<ul>
	<li>
		<a href='/hello-world'>Hello World!</a>
	</li>
</ul>
```

### Register Partials

#### Input
```
var source = "<ul>{{#people}}<li>{{> link}}</li>{{/people}}</ul>";
var context = new
{
    people = new[]
        {
            new
            {
                name = "Alan",
                id = 1
            },
            new
            {
                name = "Yehuda",
                id = 2
            }
        }
};
using (var handleBars = new Handlebars())
{
    handleBars.RegisterPartial("link",@"<a href=""/people/{{id}}"">{{name}}</a>");
    handleBars.RegisterTemplate("myTemplate", source);
    Approvals.Verify(handleBars.Transform("myTemplate", context));
}
```
#### Output
```
<ul>
	<li><a href="/people/1">Alan</a></li>
	<li><a href="/people/2">Yehuda</a></li>
</ul>
```

## HandlebarsJS

The binary ships with a resource merged version of [HandlebarsJS](http://handlebarsjs.com/). Also see the [License]( https://github.com/wycats/handlebars.js/blob/master/LICENSE).

### Current merged version

The current version included in the library is v1.3.0. If you feel a newer version should be included at any point in time please raise an issue.

### Running a custom version

If you want to run a custom version of HandlebaseJS simply place the custom `handlebars.js` in the current running directory and that file will be used instead of the merged version. 

## Icon 

<a href="http://thenounproject.com/term/mustache/19592/" target="_blank">Mustache</a> designed by <a href="http://thenounproject.com/Mattebrooks/" target="_blank">Matt Brooks</a> from The Noun Project
