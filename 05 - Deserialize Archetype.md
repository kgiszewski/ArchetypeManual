#Deserialize Archetype#

Often you may find yourself needing to work with the individual data in code.  The following example shows how to deserialize an Archetype from `IContent`:

```
using Archetype.Models;
using Newtonsoft.Json;
using Umbraco.Core;

namespace MyNamespace
{
    public class MyClass
    {
        public void MyMethod()
        {
            var contentService = ApplicationContext.Current.Services.ContentService;

            var archetypeValueAsString = contentService.GetById(1234).GetValue<string>("myArchetypeProperty");

            var archetype = JsonConvert.DeserializeObject<ArchetypeModel> (archetypeValueAsString);

            foreach (var fieldset in archetype)
            {
                var value = fieldset.GetValue<string>("text");
            }
        }
    }
}
```