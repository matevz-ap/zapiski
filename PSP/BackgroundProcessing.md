# Background Processing
- threads (general method)
- service
    - runs without the UI, by default on the same thread as the UI
- coroutines
    - light abstraction over threads that ensures blocking (of the UI thread) does not happen
- WorkManager
    - outsource your background jobs management to OS

## Threads
- UI (main) thread is started when an application is launched
    - listens for events on UI components
    - loops infinitely

- intensive work can prevent the UI thread from processing UI interaction tasks
- instead run heavy operations on background threads

### Thread and Handlers 
- create a worker thread
- put and infinite loop in it and listen for new tasks
- de-queue the tasks, for each task:
    - execute
    - report result back to the UI thread via a Handler
- break the loop to kill the thread

### Lopper, Message Queue, Handler
- **looper** keeps the thread alive in an infinite loop
    - automatically created for the UI thread
- **messageQueue** holds messages/runnables for a thread
    - message -> passing data
    - runnable -> task that is executed when the thread is free
- **handler**
    - associated with a particular thread
    - allows you to send messages/runnables to the messageQueue and process them
    - can add delay

## Services
- background service
- foreground service
    - must show a notification in the notification bar
    - more robust than background service

### Starting/Stopping
- created:
    - Context.startService()
    - Context.bindService()
- stopped:
    - stopSelf()
    - Context.stopService()

### Bound
- services started through binding, do not call onStartCommand()
- return IBinder object from onBind(Intent) so that connected clients can call the Service
- the service remains running as long as the connection is established

## Broadcast
- messages sent from components of an app
    - wrapped in Intents
- captured in an component uf registered **dynamically** in code
- captured in an app if registered **statically** in manifest

## Coroutines
- async executed code that does not block the thread it is launched from
    - can use threads
    - lighter than a thread
- managed within groups - scopes
    - globalScope - tied with the app lifecycle
- suspend function
    - contains coroutine code
    - can be paused and resumed
    - ```suspend fun heavyProc() { }```
- dispatchers -> designate threads on which a coroutine will run
    - Dispatchers.Main .IO .Default

### Launching
- launch -> does not return a result to the caller
    - used when called from traditional functions
- async -> allows the caller to wait for the result using await()
- withContext -> to launch in a different context
    - ex. from Main launch a child coroutine in the Default context

## Periodic Task Scheduling
- examples:
    - tracking physical activity
    - sync data with the server
    - reminding a user when in a particular location
- long and frequent background processing is the main reason for inefficient energy use:
    - users unaware of background processes (can't easily shut them down)
    - processes consume computational and memory resources, and prevent device from going to low-power mode

### Wake Lock
- app prevents the phone from going to a low-power sleep mode
- app needs a special permission in manifest
    - ```android.permission.WAKE_LOCK```
- acquire:
    ```
    val wakeLock: PowerManager.WakeLock =
    (getSystemService(Context.POWER_SERVICE) as PowerManager)
    .run {
        newWakeLock(PowerManager.PARTIAL_WAKE_LOCK,
        "MyApp::MyWakelockTag").apply {acquire()}
    }
    ```
- release: ```wakeLock.release()```

### AlarmManager 
- when you need tasks done at exact times between them
- do not use for:
    - periodic backups to the server (WorkManager)
    - checking for new notifications/messages from the server (Firebase messaging)

#### Types
- exactness
    - inexact -> Android decides how to group alarms in order to optimize energy use
    - exact -> executed at the prescribed time, unless the device is "sleeping"
    - exact while idle -> even if device is sleeping
    - setAlarmClock -> for actual alarm clocks (needs permission)
- clock
    - RTC - real time clock
    - ELAPSED_REALTIME - time since booted

#### Usage
- create BroadcastReceiver
- set alarm
    - define the type, starting time, repeating interval
    - supply intent that starts created BroadcastReceiver

## Doze Mode
- if device is not charging nor actively used
- periodic maintenance periods when it wakes up and performs tasks from the backlog
- during sleep:
    - wake locks ignored 
    - network access suspended 
    - AlarmManager deferred 
    - no WiFi scanning
    - Jobs not scheduled 
    - Sync adapters don't run
- programming with Doze Mode in mind, use:
    - firebase cloud messaging for communication apps
    - WorkManager for job scheduling

## WorkManager
- guaranteed deferrable execution 
- constraint-aware execution 
- respect system restriction
```
class UploadWorker(context: Context, params: WorkerParameters):
Worker (context, params) {
    override fun doWork(): Result {
        // Do the work here, e.g. upload the images.
        uploadImages()

        // Indicate whether the task finished successfully
        return Result.success()
    }
}
```

## When to use what
- best effort execution
    - ex. updating ImageView based on an API call
    - need to update UI, which may or may not be available (user navigates back from the app)
    - **Use:** coroutines, handlerThread, intentService
- guaranteed execution at the current moment
    - ex. user hits a Pay button, the transaction is processed, and the user is notified
    - we must ensure that the payment goes through and the user informed 
    - **Use:** foregroundService
- guaranteed eventual execution -> 
    - ex. reminding usr to exercise
    - should be executed every once in a while 
    - **Use:** Work Manager
- guaranteed execution at exact (periodic) times
    - ex. control an oven through an Android app
    - extremely difficult, if not possible on certain devices
    - **Use:** AlarmManager
- specialized solutions for particular use cases
    - ex. 
        - sync data with a server: Work Manager
        - download large content in the background: Download Manager
        - remind user to buy milk: Geofencing

