---
layout : post
title : "Customize Odoo View"
categories : [odoo]
published : false
---

<field name="x_mark2" 
            optional="hide"
            attrs="{'invisible': ['|', 
            ('product_id', 'not ilike', 'ZOFC'), 
            ('parent.x_workorder_id','=', '')]}"
          />