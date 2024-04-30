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


### Reference
* [Yii1 Download](https://www.yiiframework.com/download#yii1s)