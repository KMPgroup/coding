### Internalization in KMP apps

#### General rule

- Each text should be placed in locale files, whether it is inside ruby or inside javascript (coffeescript) code.
- when we take into consideration scopes there are 2 kinds of localized texts
    - **global namespaced**
    - **resource namespaced**

##### Global namespaced texts

Text which could be repeated among different pages for different resources: for example **send** or **confirm** 

##### Resource namespaced texts

Texts that are specyfic for resource: for example **send request**, **add storey**. There are stored in files named after resource (**article.yml**) inside **config/locale/ar** folder
    
#### Locale folder structure and file examples

Inside **config/locale** each language should have its own folder. Inside that folder there are one obligatory file called **default.yml** for dates, times and units. Along with default.yml comes files for global namespaced texts: **labels.yml**, **tooltips.yml**, **texts.yml**, **messages.yml** and each folder named **/resources** for resource namespaced texts.

**Example files list:**
```
/config/locales/pl/default.yml
/config/locales/pl/labels.yml
/config/locales/pl/tooltips.yml
/config/locales/pl/texts.yml
/config/locales/pl/resources/order.yml
/config/locales/pl/resources/room.yml
/config/locales/pl/resources/invoice.yml
...
```

To work there should be some changes in application.rb file:

```
config.i18n.load_path += Dir[
  Rails.root.join('config', 'locales', 'pl' ,'*.{rb,yml}').to_s,
  Rails.root.join('config', 'locales', 'pl' , "resources" ,'*.{rb,yml}').to_s
]
```

**Example global namespaced file example:**

global namespaced files are straight simple:

```
pl:
    labels:
        send: wyślij
        copy: kopiuj
        ...
```

**Example resource namespaced file example:**

structure for global namespaced files are more complex:

```
pl: 
  activerecord:
    models:
      order:
        one: Zamówienie
        few: Zamówienia
        many: Zamówień
        other: Zamówienia
    attributes:
      order:
        id: Identyfikator
        name: Imię i nazwisko
    labels:
      order:
        created_at: Data stworzenie
        ...
    tooltips:
      order:
        created_at: Data stworzenie jest to data w której zamówienie zostanie wysłane.
        ...
    ...
```

all structured is defined by standard ActiveRecord localization flow. By keeping it you can use methods as: 

```
Order.human_attribute_name :name // => Imię i nazwisko
Order.model_name.human // => order

```

### Namespaces

In both global and resource namespaced files there are several other namespaces. Usually each project contains at least following:

- **labels**: all short texts in buttons, labels, spans used among website
- **texts**: longer texts as headers and p content
- **notices** for notice informations
- **tooltips**: all tooltips and popovers

### Dealing with collections

All collections in app should be placed under labels namespace (in both global and resource namespace)

For example:

```
pl:
    labels:
        send: wyślij
        yes_no_collection:
            -
                - Nie
                - false
            -
                - Tak
                - true
            
        copy: kopiuj
        
        ...
```
