#Patterns and Anti-patterns#

* Do use Archetype when needing to group data together when you don't want to use the common Multinode Tree Picker pattern.
* Do use the built-in property value conversions by getting your data like so:

```
@foreach(var fieldset in Model.Content.GetPropertyValue<ArchetypeModel>("myProperty"))
{
    <h1>@fieldset.GetValue("headlineProperty")</h1>
}
```
* Do take care that if you use label template functions, they need to be super efficient since they may get called dozens of times.
* Don't architect your data to rely solely on Archetype.  i.e Don't plan to add 100 things to an Archetype.  This will cause editor frustration and cause performance issues.
* Don't overuse Archetype, if you can use core data types, you probably should.
* Don't nest Archetypes more that one level deep.  It makes it a pain for an editor to have to drill down.