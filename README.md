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

## View Components

* [Logic](https://github.com/teksidia/ImprovingCore/blob/master/Globomantics/Components/CreditCardRatesViewComponent.cs)
* [View](https://github.com/teksidia/ImprovingCore/blob/master/Globomantics/Views/Shared/Components/CreditCardRates/Default.cshtml)

### Usage

```
<div class="col-md-4">
    @await  Component.InvokeAsync("CreditCardRates",
       new {
           title = "Credit Card Rates",
           subtitle = "The best rates around."
       })
</div>
```

## Filters

![filter types](https://raw.githubusercontent.com/teksidia/ImprovingCore/master/_help_resources/filters.PNG)

### IActionFilter

[Example](https://github.com/teksidia/ImprovingCore/blob/master/Globomantics/Filters/ModelValidationFilter.cs) - e.g. for model validation (ModelState.IsValid)

Startup.cs
```
services.AddMvc(options =>
{
    options.Filters.Add(typeof(ModelValidationFilter));
});
```

### IExceptionFilter

[Example](https://github.com/teksidia/ImprovingCore/blob/master/Globomantics/Filters/RateExceptionFilter.cs)

```
 [RateExceptionFilter]
 public class RatesApiV2Controller : Controller
```

### IAuthorizationFilter

Good for feature toggles!

[Example](https://github.com/teksidia/ImprovingCore/blob/master/Globomantics/Filters/FeatureAuthFilter.cs)

To get DI working (for example to inject in feature settings) - use ***TypeFilter*** or ***ServiceFilter***. ServiceFilter offers more flexibility over dependency lifetimes but is more complex, so TypeFilter is the default choice.

```
[TypeFilter(typeof(FeatureAuthFilter),
        Arguments = new object[] { "Loan" })]
    public class LoanController : Controller
```

### IResourceFilter, IResultFilter

***ResultFilter*** runs logic before and after action results (which is separate to actions - ActionFilter). For example you could add additional headers to a result/response.

**ResourceFilter** wraps the MVC lifecycle - a bit like traditional middleware. For example to implement logging, caching, throttling, short circuiting etc

## Custom Validators

* [Validation](https://github.com/teksidia/ImprovingCore/blob/master/Globomantics.Core/Validation/AgeValidator.cs) attributes

* IValidatableObject to validate cross model (e.g. business logic across more than one model value) like for example if employment < 2 years, they must fill in previous employment fields - [example applied on a model](https://github.com/teksidia/ImprovingCore/blob/master/Globomantics.Core/Models/Employment.cs)

## Custom Model Binders

These are useful when the data posted to an endpoint requires transforming to a different model - a type of **Adapter**. For example if a legacy app was posting stuff in a legacy format that needs mapping to a more conventional model.

To implement: (1) [Model Binder](https://github.com/teksidia/ImprovingCore/blob/master/Globomantics/Binders/SurveyBinder.cs) (2) [Model Binder Provider](https://github.com/teksidia/ImprovingCore/blob/master/Globomantics/Binders/SurveyBinderProvider.cs)

Then add via Startup.cs

```
services.AddMvc(options =>
{
    options.ModelBinderProviders.Insert(0, new SurveyBinderProvider());
});
```

## View Location Expanders

Useful to introduce themes ect

Use [IViewLocationExpander](https://github.com/teksidia/ImprovingCore/blob/master/Globomantics/Theme/ThemeExpander.cs)

Note how config is 'injected' within _PopulateValues()_, as DI cannot be used here

```
services.Configure<IConfiguration>(Configuration);
services.Configure<RazorViewEngineOptions>(
    options => options.ViewLocationExpanders.Add(new ThemeExpander())
);
 ```

## Custom Action Results

Useful to render different responses such as CSV instead of Razor View etc

See [example](https://github.com/teksidia/ImprovingCore/blob/master/Globomantics/ActionResults/CsvResult.cs) for a CsvResult

```
 public IActionResult GetCDRates()
{
    var cdRates = rateService.GetCDRates();
    return new CsvResult(cdRates, "CD Rates");
}
```
https://drive.google.com/file/d/14ck02RTzWFgSlOkolA7UYePWXMvh9o1q/view?usp=sharing
