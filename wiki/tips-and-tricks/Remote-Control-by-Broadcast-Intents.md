**Important Notice**
Remote control by broadcast intents has to be enabled under "Settings" > "Behaviour" before Syncthing listens to broadcast intents sent by third-party automation apps.

Syncthing can be controlled externally by sending Broadcast-Intents. Applications like **Tasker**, **Llama** or **Automate** now can _start_ or _stop_ Syncthing on behalf of the user.
Use cases would be to run Syncthing only in special conditions - like at home and charging, or once every night, ...

${applicationId} = com.github.catfriend1.syncthingfork

The following intent actions are available:
* Let Syncthing Follow Run Conditions
`adb shell am broadcast -a ${applicationId}.action.FOLLOW -p ${applicationId}`

* Force Start Syncthing
`adb shell am broadcast -a ${applicationId}.action.START -p ${applicationId}`

* Force Stop Syncthing
`adb shell am broadcast -a ${applicationId}.action.STOP -p ${applicationId}`

* Request current service state
`adb shell am broadcast -a ${applicationId}.action.REQUEST_STATE -p ${applicationId}`

When remote control by broadcast is enabled, Syncthing broadcasts its current control mode and runtime state using:

`${applicationId}.action.STATE_CHANGED`

The broadcast contains the following string extras:

* `mode`: `FOLLOW`, `FORCE_START` or `FORCE_STOP`
* `run_state`: `STARTING`, `RUNNING`, `STOPPED` or `ERROR`
* `sync_state`: `UNKNOWN`, `SYNCING` or `SYNCED`

`SYNCED` is only emitted after the required completion data has been observed from Syncthing, all active local folders are idle and error-free, and the wrapper's combined local/remote completion value has remained at 100% continuously for 10 seconds. Any new synchronization activity cancels the pending stability check immediately and returns the state to `SYNCING`. `UNKNOWN` is used when Syncthing is not running, required completion data has not been initialized, or completion cannot currently be determined reliably. Disconnected devices are not treated as proof that an unreachable peer has independently confirmed synchronization.

`STATE_CHANGED` is sent when the control mode, runtime state or synchronization state changes. Sending `REQUEST_STATE` broadcasts the current state even if it has not changed.

The intents should be set to 'broadcast' rather than starting an activity of service. Note that some apps, e.g. **Llama**, are sensitive to trailing spaces so be careful not to leave any when entering the action.

Tasker example action to start Syncthing:
* Action: Send Intent
```
Action: ${applicationId}.action.START
Type: None
Mime type: [ leave empty ]
Data: [ leave empty ]
Extra: [ leave empty ]
Package: ${applicationId}
Class: [ leave empty ]
Target: Broadcast Receiver
Description: Start Syncthing
```

For the **Automate** app there is an example-flow available in the Automate-Community that demonstrates the start- and the stop-intent. Search for *Syncthing*.
