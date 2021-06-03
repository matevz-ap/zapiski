# Permissions and Notification
## Permissions 
- user data
- cost-sensitive APIs (SMS)
- system resources (camera)

### Types
- normal (OS automatically grants these at install time)
    - flashlight, vibrate, bluetooth
- dangerous (OS aks the user to grant user)
    - read contacts, send sms, location
- special (toggles in special app access page)
    -  manage storage

### Handling permissions
- prior to Marshmallow
    - user must accept permissions before the app starts
- Marshmallow
    - dangerous permissions must be granted at runtime, when the functionality is about to be used
    - ask straight away, educate up front, ask in context, educate in context

### Design
- do not request permissions unless you really need them
- show immediate benefit of granting permission
- use intents to call other apps 

## Notifications
- initiate contact with the user

### Implementation
- manage via NotificationManager
     - post notification with notify()
     - clear notification with cancel() and cancelAll()

### Design
- core functionality 
- show important information
- get feedback from the user
- show that the Service is running

Do not overuse and minimize interruptions
 - the most profound technologies are those that disappear