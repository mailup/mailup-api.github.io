# MailUp API - How to migrate from SOAP API to REST API

This document can be used as a reference for those who are in the process of migrating their application from MailUp SOAP API to MailUp REST API.
If you need more details you can create an issue on this Github project.

As you probably know, MailUp SOAP API methods belong to one of these domains: WSMailUpImport, MailUpSend, MailUpReport and MailUpManage. The following paragraphs map the methods of each domain to the MailUp REST API methods with the corresponding behaviour. There are few cases in which there is not an exact 1:1 matching, a detailed page is available for those exceptions.

* [WSMailUpImport](#wsmailupimport-methods)
* [MailUpSend](#mailupsend-methods)
* [MailUpReport](#mailupreport-methods)
* [MailUpManage](#mailupmanage-methods)

## WSMailUpImport methods

| SOAP Method Name  | Alternative method using REST API |
| ------------- | ------------- |
| ANY authentication using the request's header  | [Authentication using OAuth 2.0]() |
| GetNlLists or GetNlListsUser  | The REST API returns only the lists the logged in use has access. Start with `GET /ConsoleService.svc/Console/List`, then optionally iterate over the returned lists to get groups: `GET /ConsoleService.svc/Console/List/{ID_LIST}/Groups`   [details](https://help.mailup.com/display/mailupapi/Manage+Lists+and+Groups#ManageListsandGroups-ReadLists)|
| NewImportProcess, StartProcess and StartImportProcesses  | Any combination of these SOAP calls is replaced by a single call like `POST /ConsoleService.svc/Console/List/{id_List}/Recipients`(plain import into a MailUp "list") or `POST /ConsoleService.svc/Console/Group/{ID_GROUP}/Recipients` (when you  want to specify a "group" that belongs to a MailUp "list") [details](https://help.mailup.com/display/mailupapi/Recipients#Recipients-Subscribe/unsubscriberecipients-asynchronousimport) |
| GetProcessDetails  | `GET /ConsoleService.svc/Console/Import/{IMPORT_ID}` (if you know the import ID) or `GET /ConsoleService.svc/Console/Imports` (to get all the import tasks) [details](https://help.mailup.com/display/mailupapi/Recipients#Recipients-Readimportinformation) |
| CreateGroup  | `POST ConsoleService.svc/Console/List/{ID_LIST}/Group`  [details](https://help.mailup.com/display/mailupapi/Manage+Lists+and+Groups#ManageListsandGroups-CreateGroup) |
| GetIdUser or GetIdWsUser  | REST API can read only the ID of the user who made the request. Use `GET /ConsoleService.svc/Console/Authentication/Info` to retrieve the user ID  [details](https://help.mailup.com/display/mailupapi/Accounts) |


## MailUpSend methods
Access and account management
| SOAP Method Name  | Alternative method using REST API |
| ------------- | ------------- |
| Login or LoginFromId  | [Authentication using OAuth 2.0]() |
|Logout| No longer required  | |
|CreateList|`POST /ConsoleService.svc/Console/List`[details](https://help.mailup.com/display/mailupapi/Manage+Lists+and+Groups#ManageListsandGroups-CreateListcreatelist)|
|GetLists or GetListsUser|`GET /ConsoleService.svc/Console/List` [details](https://help.mailup.com/display/mailupapi/Manage+Lists+and+Groups#ManageListsandGroups-ReadLists)|
|GetListIdentity|No longer supported (related to a deprecated integration)||
|DeleteList|`DELETE /ConsoleService.svc/Console/List/{id_List} ` [details](https://help.mailup.com/display/mailupapi/Manage+Lists+and+Groups#ManageListsandGroups-DeleteList)|
|GetGroups|`GET /ConsoleService.svc/Console/List/{ID_LIST}/Groups` [details](https://help.mailup.com/display/mailupapi/Manage+Lists+and+Groups#ManageListsandGroups-ReadGroups)|
|GetIdUser|`GET /ConsoleService.svc/Console/Authentication/Info` [details](https://help.mailup.com/display/mailupapi/Accounts) |
|GetFields or GetFields_st|`GET /ConsoleService.svc/Console/Recipient/DynamicFields` [details](https://help.mailup.com/display/mailupapi/Recipients#Recipients-Readpersonaldatafieldsconfiguration)|


SMS
| SOAP Method Name  | Alternative method using REST API |
| ------------- | ------------- |
|CreateSMS|`POST /ConsoleService.svc/Console/Sms/List/{id_List}/Message` [details](https://help.mailup.com/display/mailupapi/Text+messages+-+SMS#TextmessagesSMS-Createamessage) |
|SendSingleSMS, SendSMSFast or SendDirectSMS|Use [Transactional SMS API]() to send single SMSs (i.e. "one to one")|
|SendSMS|`POST /ConsoleService.svc/Console/Sms/List/{id_List}/Message/{id_Message}/Send` (send to all list's recipients) or `POST /ConsoleService.svc/Console/Sms/Group/{id_Group}/Message/{id_Message}/Send` (send to a group)  [details](https://help.mailup.com/display/mailupapi/Text+messages+-+SMS#TextmessagesSMS-Sendatextmessage) |
|GetCredits or GetCreditsLists|`POST /ConsoleService.svc/Console/Authentication/Details`  [details](https://help.mailup.com/display/mailupapi/Accounts) |
|GetSMS|`POST /ConsoleService.svc/Console/Sms/List/{id_List}/Messages`  [details](https://help.mailup.com/display/mailupapi/Accounts) |
|GetSMSDeliveryStatus|`POST /ConsoleService.svc/Console/Sms/Sendings/Immediate`  [details](https://help.mailup.com/display/mailupapi/Text+messages+-+SMS#TextmessagesSMS-ManageSendQueue) |

Email
| SOAP Method Name  | Alternative method using REST API |
| ------------- | ------------- |
|SendSingleNewsletter|[Use Transactional Email API or SMTP+]() |
|CreateNewsletter|`POST /ConsoleService.svc/Console/List/{id_List}/Email`  [details](https://help.mailup.com/display/mailupapi/Email+messages#Emailmessages-Createamessage) |
|CloneMessage|`POST /ConsoleService.svc/Console/list/{id_List}/Email/{id_Message}`|
|GetNewsletters|`GET /ConsoleService.svc/Console/List/{id_List}/Emails`|
|GetMessages|A method that returns only the non-cloned email messages is no more available |
|GetNewsletterCode|`GET /ConsoleService.svc/Console/list/{id_List}/Email/{id_Message}`|
|SendMessageNL|Has to be replaced by a sequence of steps [details]() |
|SendNewsletter|`POST /ConsoleService.svc/Console/List/{id_List}/Email/{id_Message}/Send`|
|SendNewsletterFast|Newsletters & DEM: first create the email message using `POST /ConsoleService.svc/Console/List/{id_List}/Email` then send it out with `POST /ConsoleService.svc/Console/List/{id_List}/Email/{id_Message}/Send`. Transactional (one-to-one) emails: [Use Transactional Email API or SMTP+]()  |
|GetNewsletterDeliveryStatus|`GET /ConsoleService.svc/Console/List/{id_List}/Email/{id_Message}/SendHistory` (past sendouts) `GET /ConsoleService.svc/Console/Email/Sendings/Immediate` (in progress) `GET /ConsoleService.svc/Console/Email/Sendings/Deferred` (still planned)|
|GetNewsletterQueues|`GET /ConsoleService.svc/Console/Email/Sendings/Immediate` (ongoing) `GET /ConsoleService.svc/Console/Email/Sendings/Deferred` (planned) `GET /ConsoleService.svc/Console/Email/Sendings/Undefined` (unplanned)|
|StartDelivery|`POST /ConsoleService.svc/Console/Email/Sendings/{id_Sending}/Immediate` (schedule an immediate dispatch) `POST /ConsoleService.svc/Console/Email/Sendings/{id_Sending}/Deferred` (schedule a deferred dispatch)|
|RemoveNewsletterQueue|`DELETE /ConsoleService.svc/Console/Email/Sendings/{id_Sending}` Note: in case of ongoing sending you must must stop it first using `POST /ConsoleService.svc/Console/Email/Sendings/{id_Sending}/Stop`|


## MailUpReport methods

| SOAP Method Name  | Alternative method using REST API |
| ------------- | ------------- |
| Login or LoginFromId  | [Authentication using OAuth 2.0]() |
|Logout| No longer required  | 
|ReportByUser or ReportByUserEN|Has to be replaced by a [sequence of API calls]() |
|ReportByMessage or ReportByMessageEN|Use `GET /MailStatisticsService.svc/Message/{ID_MESSAGE}/List/Recipients` (delivered) `GET /MailStatisticsService.svc/Message/{ID_MESSAGE}/List/Views` (opens) `GET /MailStatisticsService.svc/Message/{ID_MESSAGE}/List/Clicks` (clicks) [details](https://help.mailup.com/display/mailupapi/Email+Statistics#EmailStatistics-GetStatisticsbyMessage) |

## MailUpManage methods

| SOAP Method Name  | Alternative method using REST API |
| ------------- | ------------- |
| Login or LoginFromId  | [Authentication using OAuth 2.0]() |
|Logout| No longer required  | 
| CreateGroup  | `POST ConsoleService.svc/Console/List/{ID_LIST}/Group`   [details](https://help.mailup.com/display/mailupapi/Manage+Lists+and+Groups#ManageListsandGroups-CreateGroup) |
|GetGroups|`GET /ConsoleService.svc/Console/List/{ID_LIST}/Groups` [details](https://help.mailup.com/display/mailupapi/Manage+Lists+and+Groups#ManageListsandGroups-ReadGroups)|
|UpdateGroup|`PUT /ConsoleService.svc/Console/List/{ID_LIST}/Group/{ID_GROUP}` [details](https://help.mailup.com/display/mailupapi/Manage+Lists+and+Groups#ManageListsandGroups-Updategroup)|
|DeleteGroup|`DELETE /ConsoleService.svc/Console/List/{ID_LIST}/Group/{ID_GROUP}` [details](https://help.mailup.com/display/mailupapi/Manage+Lists+and+Groups#ManageListsandGroups-DeleteGroup)|
|CreateList|`POST /ConsoleService.svc/Console/List` [details](https://help.mailup.com/display/mailupapi/Manage+Lists+and+Groups#ManageListsandGroups-CreateListcreatelist)|
|GetLists or GetListsUser|`GET /ConsoleService.svc/Console/List` [details](https://help.mailup.com/display/mailupapi/Manage+Lists+and+Groups#ManageListsandGroups-ReadLists)|
|UpdateList|`PUT /ConsoleService.svc/Console/List/{id_List} ` [details](https://help.mailup.com/display/mailupapi/Manage+Lists+and+Groups#ManageListsandGroups-UpdateList)|
|DeleteList|`DELETE /ConsoleService.svc/Console/List/{id_List} `[details](https://help.mailup.com/display/mailupapi/Manage+Lists+and+Groups#ManageListsandGroups-DeleteList)|
|GetIdUser|`GET /ConsoleService.svc/Console/Authentication/Info` [details](https://help.mailup.com/display/mailupapi/Accounts) |
