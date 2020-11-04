# Improving Core

Examples of how to improve .NET Core apps using extension points such as Tag Helpers, View Components, Filters etc. 

Inspired by Pluralsight 
course [Improving .NET Core MVC Apps Using Extension Points by Alex Wolf](https://app.pluralsight.com/library/courses/dotnet-core-mvc-apps-extensions/table-of-contents)

## Tag Helpers

[Data driven example](https://github.com/teksidia/ImprovingCore/blob/master/Globomantics/TagHelpers/CDTagHelper.cs)

```
<cdrate title="12 Month CD" term-length="Months12" meter-percent="50"></cdrate>
```

Don't forget e.g. ```@addTagHelper *, Globomantics``` in _ViewImports.cshtml
