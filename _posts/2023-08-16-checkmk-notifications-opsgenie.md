---
layout : post
title : "Checkmk notifications via Opsgenie"
categories : [monitor, checkmk]
published : true
---

## Configuring Opsgenie

### Add team
Create a team
![checkmk-notifications-opsgenie-00](/assets/img/2023-08/checkmk-notifications-opsgenie-00.png)

### Add Integration
Select a team > Choose the entry Checkmk under **Integrations/Add Integration.**
![checkmk-notifications-opsgenie-01](/assets/img/2023-08/checkmk-notifications-opsgenie-01.png)

### Copy the API key and save the integration

![checkmk-notifications-opsgenie-02](/assets/img/2023-08/checkmk-notifications-opsgenie-02.png)

## Configuring Checkmk

### Add User
* In Checkmk **Setup > User**, Click on `Add User`

![checkmk-notifications-opsgenie-03](/assets/img/2023-08/checkmk-notifications-opsgenie-03.png)


* Enter a `Username` and a `Full name` for this new user.

![checkmk-notifications-opsgenie-04](/assets/img/2023-08/checkmk-notifications-opsgenie-04.png)

* Leave blank **Authentication** part 
* Check **disable the login to this account** to value.
* Select **Normal monitoring user** for the Roles.
* Click on **Save** button.


### Creating New Rule Notification
After creating the new user, you will be redirected to **Users page**, again.

* Click on the **notification button** (yellow bell icon) under Actions column for the **newly created** (opsgenie) user.

![checkmk-notifications-opsgenie-05](/assets/img/2023-08/checkmk-notifications-opsgenie-05.png)

* Click on **Add Rule** on the top.

![checkmk-notifications-opsgenie-06](/assets/img/2023-08/checkmk-notifications-opsgenie-06.png)

* Enter **Opsgenie** as the Description.

![checkmk-notifications-opsgenie-07](/assets/img/2023-08/checkmk-notifications-opsgenie-07.png)

   * Select **Opsgenie** as the Notification Method.
   * Enter **API Key:** from opsgenie in API Key to use.
   * Click on **Save** button.

### Activate Changes
After saving, click on Main Menu on the left under WATO Configuration box.  You will notice an **yellow** button labeled **# Changes** on the top.

![checkmk-notifications-opsgenie-08](/assets/img/2023-08/checkmk-notifications-opsgenie-08.png)

   * Click on that button and 
   * click on **Activate on selected sites**


## Test Notifications

**Monitor > All hosts** : select checkboxs on a Host.

![checkmk-notifications-opsgenie-09](/assets/img/2023-08/checkmk-notifications-opsgenie-09.png)

In this case is the **server03**

### Fake check results
   * Select **Commands > Fake check results**
   * Select **Down** button
   
![checkmk-notifications-opsgenie-10](/assets/img/2023-08/checkmk-notifications-opsgenie-10.png)

### Confirm
Manually set check results to Down

![checkmk-notifications-opsgenie-11](/assets/img/2023-08/checkmk-notifications-opsgenie-11.png)


### Check  **Opsgenie** 

![checkmk-notifications-opsgenie-12](/assets/img/2023-08/checkmk-notifications-opsgenie-12.png)


![checkmk-notifications-opsgenie-13](/assets/img/2023-08/checkmk-notifications-opsgenie-13.png)

### Reference
* [Notifications via Opsgenie](https://docs.checkmk.com/latest/en/notifications_opsgenie.html)

* [Integrate Opsgenie with Checkmk](https://support.atlassian.com/opsgenie/docs/integrate-opsgenie-with-checkmk/)