# Editing an Archetype in Code

Often times you may want to edit an Archetype or create a new one altogether. The following example illustrates how to retrieve an Archetype from `ContentService`, edit it and save it back.

```c#
using System.Collections.Generic;
using System.Linq;
using Archetype.Models;
using Newtonsoft.Json;
using Umbraco.Core;

namespace MyApp
{
    public class MyClass
    {
        public void MyMethod()
        {
            //get a ref to services
            var services = ApplicationContext.Current.Services;

            //get a reference to the current content if an edit (or create a new node instead)
            var content = services.ContentService.GetById(1234);

            var currentArchetypeJson = content.GetValue<string>("myDoctypeAliasForArchetypeProperty");

            //deserialize into an Archetype
            var myArchetype = JsonConvert.DeserializeObject<ArchetypeModel>(currentArchetypeJson);

            //get the current fieldsets
            var currentFieldsets = new List<ArchetypeFieldsetModel>();
            currentFieldsets = myArchetype.Fieldsets.ToList();
                
            //edit some of the fieldsets or properties if you want
            var someCurrentFieldsets = currentFieldsets.Where(x => x.Alias == "someFieldsetAlias");

            foreach (var fieldset in someCurrentFieldsets)
            {
                var property = fieldset.Properties.FirstOrDefault(x => x.Alias == "somePropertyAlias");

                if (property != null)
                {
                    property.Value = "new value";
                }
            }

            //add some new fieldsets
            currentFieldsets.AddRange(    
                new List<ArchetypeFieldsetModel>(){
                //fieldset 1 of type 'myFieldsetAlias'
                new ArchetypeFieldsetModel() { 
                    Alias = "myFieldsetAlias",
                    Properties = new List<ArchetypePropertyModel>()
                    {
                        //property 1 of type 'myFieldsetAlias'
                        new ArchetypePropertyModel() {
                            Alias = "myPropertyAlias",
                            Value = ""
                        },
                        new ArchetypePropertyModel() {
                            Alias = "myPropertyAlias2",
                            Value = ""
                        },
                        new ArchetypePropertyModel() {
                            Alias = "myPropertyAlias3",
                            Value = ""
                        }
                    },
                    Disabled = false,
                },
                //fieldset 2 of type 'myFieldsetAlias'
                new ArchetypeFieldsetModel() { 
                    Alias = "myFieldsetAlias",
                    Properties = new List<ArchetypePropertyModel>()
                    {
                        new ArchetypePropertyModel() {
                            Alias = "myPropertyAlias",
                            Value = ""
                        },
                        new ArchetypePropertyModel() {
                            Alias = "myPropertyAlias2",
                            Value = ""
                        },
                        new ArchetypePropertyModel() {
                            Alias = "myPropertyAlias3",
                            Value = ""
                        }
                    },
                    Disabled = false,
                }
                //keep adding as you like
            });

            //add our edited fieldsets to the Archetype
            myArchetype.Fieldsets = currentFieldsets;

            //generate the json that needs to be saved
            var json = myArchetype.SerializeForPersistence();

            //use ContentService or MemberService etc to save it to a node
            content.SetValue("myDoctypeAliasForArchetypeProperty", json);
            services.ContentService.Save(content);
        }
    }
}
```
>Note if you want to create an Archetype instead of loading it, just instantiate a new `ArchetypeModel` instead of loading and deserializing it.
