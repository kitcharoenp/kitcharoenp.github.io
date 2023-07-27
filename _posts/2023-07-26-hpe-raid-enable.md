---
layout : post
title : "Configuring an HPE Smart Array Gen9"
categories : [ubuntu, raid]
published : false
---



[Parallel surface scan count](https://techlibrary.hpe.com/docs/iss/D2500sb_Gen10/setup_install/GUID-7EC4868E-34A1-450A-9EB5-612CE5EB0C7D.xml.html) allows the control of how many controller surface scans can operate in parallel. This is used when there is more than one logical drive on a controller. This setting allows the controller to detect bad blocks on multiple logical drives in parallel and can significantly decrease the time it takes to detect back, especially for logical drives using very large capacity drives

### iLO License Activation Keys

iLO Standard Trial License Key:
34T6L-4C9PX-X8D9C-GYD26-8SQWM

iLO 1 Advanced License Keys:
247RH-ZPJ8S-7B17D-FCE55-DDD17

iLO 2/3/4 Advanced License Keys:
35DPH-SVSXJ-HGBJN-C7N5R-2SS4W
35SCR-RYLML-CBK7N-TD3B9-GGBW2

### Reference
* [Can't Read CTR while Initializing i8042](https://support.hpe.com/hpesc/public/docDisplay?docLocale=en_US&docId=emr_na-a00041662en_us)
* [iLO License Activation Keys](https://www.chathuraariyadasa.com/ho-ilo-license-activation-keys/)

* https://community.hpe.com/t5/proliant-servers-ml-dl-sl/ilo-did-not-detect-the-agentless-management-service-when-this/td-p/7129144