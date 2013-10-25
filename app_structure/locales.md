### Internalization in KMP apps

#### General rule

- Each text should be placed in locale files, whether it is inside ruby or javascript (coffeescript).
- when we take into consideration scope there are 2 kinds of localized texts
    - global namespaced
    - resource namespaced
    
##### Global namespaced texts

Text which could be repeated among different pages for different resources: for example **send** or **confirm** 

##### Resource namespaced texts

Texts that are specyfic for resource: for example **send request**, **add storey**
    
#### Locale folder structure

Inside config/locale each language should have its own folder. Inside that folder there are one obligatory file called default.yml for dates, times and units. Along with default.yml comes files for global namespaced texts: labels.yml, tooltips.yml, texts.yml, messages.yml.
