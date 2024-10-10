---
layout : post
title : "Odoo Views : Inline Edit"
categories : [odoo]
published : true
---

### Enable Inline
**see:** `<tree>`
```xml
   <tree editable="1" edit="1">
      <field name="date" />
      <field name="project_id" required="1" />
      ...
      <field name="user_id" invisible="1" />
   </tree>
```

### Disable Inline
**see:** `<tree>`
```xml
   <tree>
      <field name="date" />
      <field name="project_id" required="1" />
      ...
      <field name="user_id" invisible="1" />
   </tree>
```
