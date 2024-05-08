---
layout : post
title : "ZurmoCRM Upgrade Yii1.1.13 to Yii1.1.29"
categories : [crm]
published : false
---

### General upgrade instructions
----------------------------
- Make a backup.
- Clean up your 'assets' folder.
- Replace 'framework' dir with the new one or point GIT to a fresh
  release and update.
- Check if everything is OK, if not — revert from backup and post
  issues to Yii issue tracker.



Upgrading from v1.1.13
----------------------

- CClientScript::registerScriptFile() and CClientScript::registerScript() methods signature changed.
  Update your subclasses that override registerScriptFile() or registerScript() if any.


### Fixed Error
----------------------------
#### Uncaught Error: Call to undefined function cleanAndSanitizeScriptHeader()
*/var/www/html/zurmo/app/protected/core/components/ClientScript.php:94*

```php
 public function render(& $output)
{
    if ($this->isAjaxMode())
    {
        $this->removeAllPageLoadedScriptFilesWhenRenderingInAjaxMode();
    }
    parent::render($output);
    if (!$this->isAjaxMode())
    {
        cleanAndSanitizeScriptHeader($output); // Fatal error
    }
}
```
### CException: CHttpRequest is unable to determine the request URI.

```php
throw new CException(Yii::t('yii','CHttpRequest is unable to determine the request URI.'));
```
* **Action:** `php zurmoc updateSchema super`
* **File:** framework/web `CHttpRequest.php:584`

#### Solution
* **Class::method** `ZurmoHttpRequest::isTrustedRequest()`

```php
protected function isTrustedRequest()
{
  // Kitcharoenp@gmail.com@2023-09-19 
  // Fixed: CException: CHttpRequest is unable to determine the request URI
  $_SERVER['REQUEST_URI'] = '/index.php';
  // Fix end

  $requestedUrl   = Yii::app()->getRequest()->getUrl();

```


#### [warning] [system.web.CClientScript] There is no CClientScript package: jquery-animate-sprite


### Fatal error:  Interface 'RedBean_ILogger' not found
```
$ php zurmoc updateSchema super
    Starting schema update process.
Info  - Searching for models
PHP Fatal error:  Interface 'RedBean_ILogger' not found in /var/www/oamp00/public_html/zurmo/app/protected/core/models/ZurmoRedBeanQueryFileLogger.php on line 42
```

### 2024/05/01 16:30:40 [warning] [system.web.CClientScript] There is no CClientScript package: jquery-animate-sprite

### 2024/05/01 16:34:42 [warning] [application.modules.workflows.utils.WorkflowActionsUtil.processAfterSave] Exception class: NotSupportedException Thrown with message: Invalid action type: Update

### 

```
#0 /var/www/oamp00/public_html/zurmo/app/protected/modules/zurmo/models/OwnedSecurableItem.php(461): SecurableItem->checkPermissionsHasAnyOf(16, Object(User))
#1 /var/www/oamp00/public_html/zurmo/app/protected/modules/zurmo/models/OwnedSecurableItem.php(194): OwnedSecurableItem->checkPermissionsHasAnyOf(16)
#2 /var/www/oamp00/public_html/zurmo/app/protected/modules/zurmo/models/OwnedSecurableItem.php(178): OwnedSecurableItem->ownerChange(Object(User))
#3 /var/www/oamp00/public_html/zurmo/app/protected/modules/workorders/models/Workorder.php(316): OwnedSecurableItem->__set('owner', Object(User))
#4 /var/www/oamp00/public_html/zurmo/app/protected/modules/workorders/models/Workorder.php(291): Workorder->resolveOwnerByStatus()
#5 /var/www/oamp00/public_html/zurmo/app/protected/core/models/RedBeanModel.php(1892): Workorder->beforeSave()
#6 /var/www/oamp00/public_html/zurmo/app/protected/modules/zurmo/models/Item.php(123): RedBeanModel->save(true, NULL)
#7 /var/www/oamp00/public_html/zurmo/app/protected/modules/workorders/utils/WorkorderDataModelUtil.php(109): Item->save()
```



```
2024/05/01 16:43:26 [warning] [application.modules.workflows.utils.WorkflowActionsUtil.processAfterSave] Exception class: NotSupportedException Thrown with message: Invalid action type: Update
#0 /var/www/oamp00/public_html/zurmo/app/protected/modules/workflows/utils/WorkflowActionsUtil.php(83): WorkflowActionProcessingHelper->processNonUpdateSelfAction()
#1 /var/www/oamp00/public_html/zurmo/app/protected/modules/workflows/utils/SavedWorkflowsUtil.php(168): WorkflowActionsUtil::processAfterSave(Object(Workflow), Object(StockTransfer), Object(User))
#2 /var/www/oamp00/public_html/zurmo/app/protected/modules/workflows/observers/WorkflowsObserver.php(121): SavedWorkflowsUtil::resolveAfterSaveByModel(Object(StockTransfer), Object(User))
#3 /var/www/oamp00/public_html/zurmo/yii/framework/base/CComponent.php(559): WorkflowsObserver->(Object(CEvenprocessWorkflowAfterSavet))
#4 /var/www/oamp00/public_html/zurmo/app/protected/core/models/RedBeanModel.php(2080): CComponent->raiseEvent('onaftersave', Object(CEvent))
#5 /var/www/oamp00/public_html/zurmo/app/protected/core/models/RedBeanModel.php(2059): RedBeanModel->onAfterSave(Object(CEvent))
#6 /var/www/oamp00/public_html/zurmo/app/protected/modules/zurmo/models/Item.php(199): RedBeanModel->afterSave()
#7 /var/www/oamp00/public_html/zurmo/app/protected/modules/zurmo/models/SecurableItem.php(532): Item->afterSave()
#8 /var/www/oamp00/public_html/zurmo/app/protected/modules/zurmo/models/OwnedSecurableItem.php(243): SecurableItem->afterSave()
#9 /var/www/oamp00/public_html/zurmo/app/protected/modules/stockTransfers/models/StockTransfer.php(429): OwnedSecurableItem->afterSave()
#10 /var/www/oamp00/public_html/zurmo/app/protected/core/models/RedBeanModel.php(2013): StockTransfer->afterSave()
#11 /var/www/oamp00/public_html/zurmo/app/protected/modules/zurmo/models/Item.php(123): RedBeanModel->save(true, NULL)
#12 /var/www/oamp00/public_html/zurmo/app/protected/modules/stockTransfers/utils/StockTransferDataModelUtil.php(301): Item->save()
#13 /var/www/oamp00/public_html/zurmo/app/protected/modules/stockTransfers/utils/StockTransferDataModelUtil.php(27): StockTransferDataModelUtil::updateStatusWhenStatusIsInitial(Object(StockTransfer))
#14 /var/www/oamp00/public_html/zurmo/app/protected/modules/stockTransfers/controllers/DefaultController.php(429): StockTransferDataModelUtil::resolveForwardStatusValue(Object(StockTransfer))
#15 [internal function]: StockTransfersDefaultController->actionApprove('151489')

```

### Reference
* [Yii1 Download](https://www.yiiframework.com/download#yii1s)