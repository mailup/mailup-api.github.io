# MailUp API - How to migrate from SOAP API to REST API

This document can be used as a reference for those who are in the process of migrating their application from MailUp SOAP API to MailUp REST API.
If you need more details you can create an issue on this Github project.

As you probably know, MailUp SOAP API methods belong to one of these domains: WSMailUpImport, MailUpSend, MailUpReport and MailUpManage. The following paragraphs map the methods of each domain to the MailUp REST API methods with the corresponding behaviour. There are few cases in which there is not an exact 1:1 matching, a detailed page is available for those methods.



## WSMailUpImport methods

| SOAP Method Name  | Alternative method using REST API |Reference|
| ------------- | ------------- |-----------|
| ANY authentication using the request's header  | Authentication using OAuth 2.0  | [Read more]() |
| GetNlLists or GetNlListsUser  | The REST API returns only the lists the logged in use has access. Start with `GET /ConsoleService.svc/Console/List`, then optionally iterate over the returned lists to get groups: `GET /ConsoleService.svc/Console/List/{ID_LIST}/Groups`   | [Read Lists](https://help.mailup.com/display/mailupapi/Manage+Lists+and+Groups#ManageListsandGroups-ReadLists)|
| NewImportProcess, StartProcess and StartImportProcesses  | Any combination of these SOAP calls is replaced by a single call like `POST /ConsoleService.svc/Console/List/{id_List}/Recipients`(plain import into a MailUp list) or `POST /ConsoleService.svc/Console/Group/{ID_GROUP}/Recipients` (when adding to an existing group of a MailUp list)  | [Start bulk import](https://help.mailup.com/display/mailupapi/Recipients#Recipients-Subscribe/unsubscriberecipients-asynchronousimport) |
| GetProcessDetails  | `GET /ConsoleService.svc/Console/Import/{IMPORT_ID}` (if you know the import ID) or `GET /ConsoleService.svc/Console/Imports` (to get all the import tasks) | [Read import status](https://help.mailup.com/display/mailupapi/Recipients#Recipients-Readimportinformation) |
| CreateGroup  | `POST ConsoleService.svc/Console/List/{ID_LIST}/Group`  | [Create group](https://help.mailup.com/display/mailupapi/Manage+Lists+and+Groups#ManageListsandGroups-CreateGroup) |
| GetIdUser or GetIdWsUser  | REST API can read only the ID of the user who made the request. Use `GET /ConsoleService.svc/Console/Authentication/Info` to retrieve the user ID  | [Get user ID](https://help.mailup.com/display/mailupapi/Accounts) |


## MailUpSend methods
Access and account management
| SOAP Method Name  | Alternative method using REST API |Reference|
| ------------- | ------------- |-----------|
| Login or LoginFromId  | Authentication using OAuth 2.0  | [Read more]() |
|Logout| No longer required  | |


SMS
| SOAP Method Name  | Alternative method using REST API |Reference|
| ------------- | ------------- |-----------|
|CreateSMS|`POST /ConsoleService.svc/Console/Sms/List/{id_List}/Message`  | [Create SMS](https://help.mailup.com/display/mailupapi/Text+messages+-+SMS#TextmessagesSMS-Createamessage) |
|SendSingleSMS, SendSMSFast or SendDirectSMS|Use Transactional SMS API to send single SMSs (i.e. "one to one")  | [Read more]() |
|SendSMS|`POST /ConsoleService.svc/Console/Sms/List/{id_List}/Message/{id_Message}/Send` (send to all list recipients) or `POST /ConsoleService.svc/Console/Sms/Group/{id_Group}/Message/{id_Message}/Send` (send to a group)  | [Send bulk SMS](https://help.mailup.com/display/mailupapi/Text+messages+-+SMS#TextmessagesSMS-Sendatextmessage) |
|GetCredits or GetCreditsLists|`POST /ConsoleService.svc/Console/Authentication/Details`  | [Read credits](https://help.mailup.com/display/mailupapi/Accounts) |
|GetSMS|`POST /ConsoleService.svc/Console/Sms/List/{id_List}/Messages`  | [Get SMS](https://help.mailup.com/display/mailupapi/Accounts) |
|GetSMSDeliveryStatus|`POST /ConsoleService.svc/Console/Sms/Sendings/Immediate`  | [Read/manage SMS queue](https://help.mailup.com/display/mailupapi/Text+messages+-+SMS#TextmessagesSMS-ManageSendQueue) |

Email
| SOAP Method Name  | Alternative method using REST API |Reference|
| ------------- | ------------- |-----------|
|SendSingleNewsletter|Use Transactional Email API or SMTP+  | [Read more]() |
|GetSMS|`POST /ConsoleService.svc/Console/Sms/List/{id_List}/Messages`  | [Get SMS](https://help.mailup.com/display/mailupapi/Accounts) |

