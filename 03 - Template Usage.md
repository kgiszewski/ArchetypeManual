#Template Usage#

Getting the data out of Archetype is easy.  Consider the following:

```
@inherits Umbraco.Web.Mvc.UmbracoTemplatePage
@using Archetype.Models;
@using Archetype.Extensions;
@{
    Layout = null;
}

//use case #1 - Covers a single Archetype
@foreach (var fieldset in Model.Content.GetPropertyValue<ArchetypeModel>("a1"))
{
    <!-- get a property by name -->
    <div>@fieldset.GetValue("mntp")</div>
    
    <!-- or -->
    
    <!-- if using a property editor with a value converter -->
    <div>@fieldset.GetValue<IEnumerable<IPublishedContent>>("mntp")</div>
}

//use case #2 - Covers nested Archetypes
@foreach (var fieldset in Model.Content.GetPropertyValue<ArchetypeModel>("a1"))
{
    <div>@fieldset.GetValue("firstName")</div>
    
    <!-- get nested Archetype -->
    foreach(var nestedFs in fieldset.GetValue<ArchetypeModel>("movieList")){
        <h3>@nestedFs.GetValue("title")</h3>
        <div>@nestedFs.GetValue("text")</div>
    }
}

//use case #3; be able to iterate over the properties, however this will not pass value through converters
@foreach (var fieldset in Model.Content.GetPropertyValue<ArchetypeModel>("a1"))
{
    <!-- will not go through value converters -->
    foreach (var prop in fieldset.Properties)
    {
        <div>@prop.Value</div>
    }
}
```

##Page Builder##
You can even render partials based on the alias of the fieldset easily.   If you have an Archetype that has more than one fieldset configured with aliases of `RichtextModule` and `GalleryModule`, then you can render them dynamically with one line of code: 

`@Html.RenderArchetypePartials(Model.Content.GetPropertyValue<ArchetypeModel>("modules"))`

>This example assumes you named your property `modules` on your document type.

Each partial would have to be defined in the `~/Views/Partials/Archetype` folder like so:

```
@* File ~/Views/Partials/Archetype/RichtextModule.cshtml *@
@inherits Umbraco.Web.Mvc.UmbracoViewPage<ArchetypeFieldsetModel>
@using Archetype.Models;

<div class="row">
    <div class="content col-lg-12">
        @Html.Raw(Model.GetValue("text")
    </div>
</div>
```

```
@* File ~/Views/Partials/Archetype/GalleryModule.cshtml *@
@inherits Umbraco.Web.Mvc.UmbracoViewPage<ArchetypeFieldsetModel>
@using Archetype.Models;

<div class="row">
    <div class="content col-lg-12">
        <!-- do something here for a gallery -->
    </div>
</div>
```



The next snippet is the final template:

```
@using Archetype.Extensions
@using Archetype.Models
@using MyNamespace.UmbracoExtensions.BizMag.Models
@inherits Umbraco.Web.Mvc.UmbracoTemplatePage
@{
    Layout = "~/Views/BizMag/BizmagBase.cshtml";
}

<!-- begin:container -->
<div class="container content-wrapper article">
    <!-- begin:main-section -->
    <div class="row">
        <div class="col-md-12">
            @Html.Partial("~/Views/BizMag/Partials/Breadcrumbs.cshtml")
        </div>
    </div>
    <div class="row">
        <div class="col-md-12">
            <h1>@Model.Content.Name</h1>
        </div>
    </div>
    <div class="row">

        <!-- begin:article -->
        <div class="col-md-8">

            @* this will read the entire archetype and render a partial named the same as the fieldset alias *@         

            @Html.RenderArchetypePartials(Model.Content.GetPropertyValue<ArchetypeModel>("modules"))

            @Html.Partial("~/Views/BizMag/Partials/ContactForm.cshtml", new ContactUsModel())

        </div>
        <!-- end:article-->
        <!-- begin:sidebar -->
        <div class="col-md-4">
            <!-- begin:widget-sidebar -->
            <div class="content-article">
                @Html.Partial("~/Views/BizMag/Partials/Archives.cshtml")
            </div>
            <!-- end:widget-sidebar -->
        </div>
        <!-- end:sidebar -->
    </div>
    <!-- end main-section-->
</div>
```
