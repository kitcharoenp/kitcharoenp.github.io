---
layout : post
title : "Odoo Edit Country and Address Formatting"
categories : [Odoo]
published : true
---

### Menu
To change your Default Taxes, go to Contacts ‣ **Configuration ‣ Localization ‣ Countries**

![odoo-edit-country-and-states-list-00](/assets/img/blog/2023-11-30-odoo-edit-country-and-address-formatting-00.png)


### Configuration
![odoo-edit-country-and-states-list-01](/assets/img/blog/2023-11-30-odoo-edit-country-and-address-formatting-01.png)


### [Address Formatting](https://www.odoo.com/forum/help-1/customize-document-layout-202329)
Update format of address for specific country. Go to the menu **Contacts -> Localization -> Countries -> select countries** and open the form view.

Under the **"Advanced Address Formatting"**.
Modify the field `Layout in Reports` and set your address format

```
%(street)s
%(street2)s
%(city)s %(state_code)s %(zip)s
%(country_name)s
```

### Reference
* [Edit Country and States list](https://www.odoo.com/forum/help-1/edit-country-and-states-list-68625)

* [Address Formatting](https://www.odoo.com/forum/help-1/customize-document-layout-202329)

