# Activity, Lifecycle, Intents

## Basic components
- Activity -> graphical user interface (GUI)
- Service -> performs background processing
- BroadcastReceiver -> subscribes to events of interest
- Intent -> communicates an intention to perform and action
- ContentProvider -> encapsulates and exposes data

## What is an Application ?
- collection of components that are packaged together
    - can be instantiated and ran as needed
- .apk is what we usually refer to when we say "application"

## Activity
- usually implements a single focused task a user can do
- usually more than ne activity per application
- interface itself is usually defined in a separate layout file (XML)
- user's interaction influences the activity that is going to be shown 
    - activity launching via Intents in the code

## Activity Lifecycle
### Activity state
- **Active/Running** -> in the foreground, visible, user interacting
- **Paused** -> lost focus but still visible, maintains state and member information
- **Stopped** -> obscured by another activity, retains state and member information, however, no longer visible, cane be terminated by the OS when needed

Activity exists: onCreate() -> onDestroy()
Activity visible: onStart() -> onStop()
Activity in foreground: onResume() -> onPause()

### onCreate()
- sets up the initial state:
    - create and configure views
    - set Activity's content view (instruct the Activity to show something to the user)
    - bind data to lists
    - super.onCreate() -> hides some complex code that must be called in order to instantiate the Activity properly
- also get a Bundle with the Activity's previous state

### onStart()
- called when the activity is becoming visible
- setup state relevant for visible-only behavior
    - register certain BroadcastReceivers
- load persistent application state

### onRestart()
- called if activity is becoming visible, after being stopped
- will be followed by onStart() and onResume()

### onResume()
- called when the activity is visible and is about to start interacting with the user
- start foreground-only activities (get user location and show it on the map)

### onPause() 
- called when the Activity loses focus, and another is about to start
- used to commit unsaved changes to persistent data, stop animations, CPU-intensive processing
- next activity will not start until this method returns

### onStop()
- called when activity is no longer visible
- release resources that are not needed while the activity is not visible
- perform CPU-heavy shutdown operations
- activity still exists, but is not connected to Window Manager
    - returning (via onRestart()) will set the Activity state bak to the last seen value

### onDestroy()
- happens when:
    - finish() finish is called
    - OS calls it
    - back button is pressed
- used to release resources such as Threads that are associated with the activity
- may not be called if Android kills your application

### Placing app functionalities
- instantiate member variables of the class -> onCreate()
- (un)register listeners:
    - location listener for showing a user's location on a map -> onResume()/onPause()
    - location listener for tracking user location -> onStart()/onStop()
- kill threads that the activity has spawned -> onDestroy()

## Saving Activity State
- onSaveInstanceState() -> called when the Activity gets stopped to store state in a Bundle
    - not a part of the lifecycle !
- restore in onRestoreInstanceState() or in onCreate()

## Starting Activities
- pass the Intent to one of the following methods:
    - startActivity(): launches Activity described by Intent
    - startActivityForResult(): expects a result that will be returned via onActivityResult(). The called activity can set result via setResult() method

## Task
- collection of Activities that users interact with when performing a certain job
    - activities need not be from the same application
- **Backstack** (first in first out)
    - more than one instance of an Activity can be on the backstack
    - when home is pressed, the current activity is stopped, its task goes into the background
    - if the user later resumes the task, the activity at the top of the stack is started

## Intent
- data structure representing
    - operation to be performed or
    - an event that has occurred
- serve as a glue between activities
    - constructed by a component that wants some work to be done
    - received by an Activity that can perform that work
- fields: action, data, category, type, component, extras