# ForestMusic Android Device QA

## Canonical startup

From the project root:

```powershell
.\scripts\android-device-qa.ps1
```

Use the following only when appropriate:

```powershell
.\scripts\android-device-qa.ps1 -Build
.\scripts\android-device-qa.ps1 -Logcat
```

`-Build` is for native/config/dependency changes. Normal JS/TS-only QA can
reuse the installed development build. `-Logcat` prints a bounded logcat
snapshot filtered to the discovered application PID.

## Verified Metro behavior

The standard local Windows command is:

```powershell
npx expo start --dev-client --host lan --port 8081
```

The script checks the host IPv4 socket, checks `/status`, runs:

```powershell
adb reverse tcp:8081 tcp:8081
adb shell curl -s http://127.0.0.1:8081/status
```

and launches the discovered Expo scheme with a dev-client URL containing
`http://127.0.0.1:8081`. Do not use Expo `a` and do not put a LAN IP into that
URL. On the verified workstation, `--localhost` bound only to `::1:8081` and
failed the phone-side reverse connection.

## Diagnostic layers

When the script stops, diagnose only the reported layer:

`SOURCE -> SDK -> DEVICE -> BUILD -> INSTALL -> DUMPSYS -> METRO -> IPv4 ->
ADB REVERSE -> PHONE /STATUS -> DEV CLIENT -> REAL UI -> PID LOGCAT`

Manual commands are a troubleshooting aid, not the normal startup path.

## Device priority

Prefer one connected real Android device. Do not start an AVD when a real
device is available. AVDs are useful for smoke checks, but physical bottom
safe-area and gesture/navigation behavior require a real phone.

## Safety

The canonical script does not clean Gradle, delete dependencies/native
folders/caches, run `npm install`, run `prebuild --clean`, change source or
package configuration, switch to 8082/8083, launch a heavy AVD, kill unknown
processes, reset/clean Git or force-push. Unknown state is a STOP with a
useful diagnostic.

The final script PASS proves device connectivity and dev-client startup. It
does not prove visual correctness, gameplay or screen-level QA.
