---
layout: post
title: "Alfresco : Advanced Workflows"
published: false
categories: [alfresco]
---

### Out-of-the-box workflow options in Alfresco
>Alfresco has two options for implementing workflows within the product. For very simplistic workflows, non-technical end-users can leverage Alfresco's Basic Workflow functionality. For more complex needs, Alfresco has Advanced Workflow functionality.

Alfresco is an “embedded workflow engine” example. It embeds Activiti, which is an
example of a standalone engine.


### Step
* [Getting Started with the Alfresco Maven SDK](https://ecmarchitect.com/alfresco-developer-series)
* [Custom Content Types](https://ecmarchitect.com/alfresco-developer-series-tutorials/content/tutorial/tutorial.html)


### Setup Eclipse

For fixed a error
```
$ cat ~/.xinputrc
export GTK_IM_MODULE=ibus
export XMODIFIERS=@im=ibus
export QT_IM_MODULE=ibus
# im-config(8) generated on Mon, 18 Mar 2019 12:39:52 +0700
# run_im xim
# im-config signature: 8e235abbb3df7d3ba78a3a444004a27e  -
```

There are two model definition files related to this. One is called called bpmModel.xml. It resides in your Alfresco web application root under WEB-INF/classes/alfresco/model. The other is called workflowModel.xml and it resides under WEB-INF/classes/alfresco/workflow.

So, all tasks in which there are Alfresco web client **user interactions must be given a `form key`** that corresponds to the name of a workflow content type.



### Reference
* [Creating Custom Advanced Workflows in Alfresco](https://ecmarchitect.com/alfresco-developer-series-tutorials/workflow/tutorial/tutorial.html)
* [Advanced Workflow สำหรับ Alfresco](https://thaiopensource.org/advanced-workflow-%e0%b8%aa%e0%b8%b3%e0%b8%ab%e0%b8%a3%e0%b8%b1%e0%b8%9a-alfresco/)
* [Free BPMN modelling tools](https://bpmtips.com/free-bpmn-modelling-tools-2018-edition/)
* [การสร้าง Alfresco Workflow โดย Activiti BPM 5.8](https://thaiopensource.org/%e0%b8%81%e0%b8%b2%e0%b8%a3%e0%b8%aa%e0%b8%a3%e0%b9%89%e0%b8%b2%e0%b8%87-alfresco-workflow-%e0%b9%82%e0%b8%94%e0%b8%a2-activiti-bpm-5-8/)
* [Alfresco Web Quick Start](https://docs.alfresco.com/5.2/concepts/WCM-intro.html)
