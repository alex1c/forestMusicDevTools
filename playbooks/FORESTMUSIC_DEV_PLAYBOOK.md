# FORESTMUSIC DEV PLAYBOOK

## Canonical DevTools v1.1.0 controlled update

This file preserves the operational source Playbook. The rules in this
section are the current canonical updates and take precedence where older
sections conflict with them.

### Standard physical-device startup

From a project root, use the reusable script first:

```powershell
.\scripts\android\android-device-qa.ps1
```

Use `-Build` after native/config/dependency changes and `-Logcat` for a
bounded app-scoped diagnostic snapshot. Manual commands remain diagnostic
fallback only.

The standard Windows Metro command is:

```powershell
npx expo start --dev-client --host lan --port 8081
```

The dev client is launched explicitly through `127.0.0.1:8081` after
`adb reverse`; do not press Expo `a` and do not put a LAN IP in the dev-client
URL. On the verified Windows workstation, `--localhost` bound Metro to
`::1:8081` and failed the phone-side reverse connection.

One active project owns the standard Metro port 8081. Identify its PID,
query /status, and establish the project root before reusing an existing
listener. The QA script reuses only a healthy Metro tied to this project; a
confirmed other-project or unknown owner is a STOP. Never kill an unknown
Node process. A successful launch and app PID do not prove that the intended
project bundle is visible; verify app/project identity.

If Metro and the app PID pass but the screen is blank: force-stop the package,
explicitly relaunch its dev-client deep link to this project's Metro, watch
for Android Bundled and confirm the project root, then collect bounded
app-PID logcat if still blank. Do not begin by reinstalling dependencies,
clearing caches, running clean prebuild, or deleting app data.

### Current product and layout defaults

- Reserve a `BannerSlot` early on suitable user-facing screens. The default is
  Home, lists, statistics, settings, reminders, About and informational
  screens; training/onboarding is not an ad surface.
- Place the banner at the bottom of the usable app area:
  `CONTENT -> BANNER -> SAFE AREA/INSET -> SYSTEM AREA`.
- A banner must not float, overlay content, hide under navigation, or push a
  critical control below the viewport. Gameplay/work areas require an
  explicit per-app decision based on measured interaction constraints.
- Use real safe-area/window insets and measured layout spacing. Never repair
  bottom layout with device-specific `translateY` or magic offsets.
- Every suitable app includes an active `Other our apps` link in About (and
  optionally Settings/More) to the configured ForestMusic/RuStore developer
  page. If the canonical URL is not known, leave a clearly marked
  configurable placeholder and verify it before release.
- During planning, explicitly decide whether the product has a natural
  reminder use case. If yes, prefer useful configurable, cancellable,
  reconciled reminders; do not add spam reminders.
- Fresh reminders default OFF. Ask for notification permission only after
  explicit opt-in; never request POST_NOTIFICATIONS at app startup. A denied
  permission must not leave a false ON state.
- Give each reminder an explicit typed destination for foreground,
  background, and cold-start taps; guard duplicate navigation. Keep
  notification payloads free of personal data and do not put ads or an
  immediate interstitial in the notification return flow.
- A dev test notification, when needed, is DEV-only, separately identified,
  short-delay, routed like the production reminder, and must not mutate user
  preferences, completion, streak, or statistics.
- A Reminder/Notifications screen normally receives the same bottom banner
  treatment and safe-area verification.

### Bootstrap and generic lessons

New projects consult this repository before implementation, pin the DevTools
version used, copy the standard QA script, establish strict TypeScript/tests/
lint, and plan safe area, onboarding, reminders, banners and About early.

Generic defect flow remains: `ROOT CAUSE -> MINIMAL FIX -> REGRESSION TEST ->
UPDATE PLAYBOOK/DEVTOOLS`. Application-specific lessons stay in the project;
cross-project lessons belong here.

For capture and release detail, use the canonical
[RuStore screenshot workflow](RUSTORE_SCREENSHOTS.md),
[Android device QA](ANDROID_DEVICE_QA.md), and
[release checklist](../checklists/RUSTORE_RELEASE.md).

Версия: 2026-09-10

Единый регламент Android / RuStore проектов ForestMusic.

## 1. Базовые принципы

-   Сначала простое и стабильное решение.
-   Не менять работающий процесс без причины и не повторять уже
    неработавшие варианты.
-   Минимум вопросов пользователю; Cursor делает основную разработку,
    Codex --- важные checkpoint.
-   GitHub --- точка истины.
-   Новая серьёзная проблема: root cause → fix → regression test →
    правило в Playbook.
-   Во всех приложениях обязательно «Обучение».

## 2. Git и каталоги

-   Cursor: `D:\PetProject\<repo>`
-   Codex/AVD: `D:\petProject\<repo>`
-   Перед сменой ПК: `git status`, `git fetch origin`,
    `git pull --ff-only origin main`.
-   Не использовать force push.
-   Не переносить проект во временные/короткие пути ради диагностики.

## 3. Metro / AVD

-   Один Metro, основной порт `8081`; не лечить запуском второго на
    `8082`.
-   Основной AVD: `ForestMusic_Fast_API35`.
-   Проверенный flow: лёгкий AVD → один Metro → native build → install →
    launch.
-   `Pixel_10 / API 37` --- только редкий final/API-specific QA.
-   При native modules не тратить время на Expo Go: использовать
    native/dev build.

## 4. REAL DEVICE BOTTOM SAFE AREA --- КРИТИЧНО

AVD не считается достаточной проверкой нижней зоны. На реальном Android
gesture/navigation bar может перекрывать или прижимать элементы к низу.

-   Важные CTA не размещать вплотную к физическому низу.
-   Учитывать реальные bottom safe-area/window insets, а не
    фиксированный `paddingBottom`.
-   Особенно: «Сохранить», «Готово», «Продолжить», «Далее», «Завершить»,
    FAB, bottom-sheet actions, нижние панели.
-   CTA должен полностью находиться выше system navigation/gesture area
    и иметь комфортный отступ.
-   Bottom sheets тоже учитывают нижний inset.
-   Перед релизом ключевые нижние CTA обязательно проверить хотя бы на
    одном физическом Android-телефоне.

## 5. Keyboard / числа

-   Android forms используют корректный resize/insets flow.
-   Поле и Save не перекрываются клавиатурой.
-   Decimal input принимает `6,4` и `6.4`.
-   Не показывать floating-point мусор; использовать централизованные
    форматтеры.

## 6. SQLite

-   Никакого unsafe `Promise.all` к одному `NativeDatabase`; запросы
    последовательные/serialized.
-   Внутри `withTransactionAsync` не re-queue SQL в ту же FIFO-очередь:
    transaction executor напрямую. Держать regression test.
-   Для таймеров source of truth --- timestamps SQLite
    (`now - startedAt`).
-   События через полночь агрегировать по пересечению с локальным
    календарным днём.
-   React keys для game/session lifecycle должны быть стабильной семантической
    identity (mode/level, date key, session ID), а не изменяемыми полями
    persistence вроде updatedAt/autosave counter.

## 7. Фото / файлы

-   Не хранить BLOB в SQLite.
-   Managed filesystem + URI/path.
-   Не удалять оригинал из галереи.
-   Managed file удалять только без оставшихся ссылок.
-   Missing file → fallback, не crash.

## 8. Backup / Restore

-   Versioned manifest, portable DB representation, относительные media
    paths, URI remap.
-   Restore: validate → temp → schema check → rollback snapshot →
    restore → migrations → reminder reconciliation → cleanup.
-   Persistence changes требуют versioned schema и явной backward-compatible
    migration; corrupt/incomplete data возвращает безопасные defaults, не
    стирая несвязанный user progress.
-   Защита от ZIP path traversal.
-   Platform notification IDs после restore пересоздавать.

## 9. Реклама

-   Монетизировать заметно, но не раздражающе; ориентир --- один баннер
    на основной информационный экран.
-   Interstitial не показывать в
    sleep/feeding/diaper/health/forms/backup/onboarding/training.
-   Хорошая точка: PDF создан → optional interstitial → ready screen.
-   Максимум примерно один interstitial/session; no-fill/error не
    блокирует функцию; Share/повторное открытие PDF без второй рекламы.
-   Rewarded не использовать без естественного сценария.

## 10. AppMetrica

Логировать факт использования функций, но не имена, даты рождения, фото,
заметки, лекарства, симптомы, температуру, объёмы/длительности,
PDF/backup content. Analytics failure не блокирует UI.

## 11. Иконка

ChatGPT создаёт master → пользователь сохраняет `assets/icon_gpt.png` →
Cursor делает Android derivatives и `release-artifacts/icon-512.png`
512×512. Master --- source of truth.

## 12. Скриншоты RuStore

До production ads снять на физическом устройстве clean masters 5–8 сильных
экранов и сохранить originals без изменений. При уже подключённых ads
использовать только DEV Screenshot QA Mode с теми же BannerSlot/safe-area
размерами, без ad requests и автоматических interstitial; production должен
не допускать этот режим. В Windows PowerShell 5.1 не делать
`adb exec-out screencap -p > file.png`: захватывать через удалённый PNG на
устройстве и `adb pull`. Не ретушировать рекламные материалы.

Канонический порядок захвата, crop policy и QA описаны в
[RuStore Screenshot Workflow](RUSTORE_SCREENSHOTS.md). Store portrait:
1080×1920, 9:16. Для master 1080×2400 удалить ровно 480 строк
асимметричным crop без stretch. Если важный UI не помещается — recapture.
Исходники остаются нетронутыми; contact sheet — только QA, не для загрузки.

## 13. Release order

Функционал → UX/safe area/banner geometry → reminders/onboarding →
physical functional QA → clean screenshot masters → ads/analytics →
release permissions/config → финальные RuStore images → source freeze →
final AAB from clean HEAD==origin/main → AAB manifest/signing/checksum audit
→ PEPK if RuStore requests → upload.

Любой source commit после AAB build, даже DEV-only tooling, требует
повторной сборки из нового final SHA. До release build очистить DEV/QA env
flags и проверить, что они отсутствуют. Final AAB подтверждать через
bundletool/equivalent: package, versions, min/target SDK, permissions,
signer fingerprints, SHA256/size и sidecar.

## 14. Production signing

Production keystore создаёт только пользователь. Хранить
`D:\secure\android-signing\<repo>\`.

Рекомендуемая структура вне Git: release JKS и signing.properties с
ограниченным доступом.

Пароли читать через `.Trim()`, не логировать/коммитить. После
`bundleRelease` проверить alias, SHA1/SHA256 сертификата и SHA256 AAB.
Несовпадение fingerprint → STOP.
Пароли не хранить в отдельных текстовых файлах, не выводить properties и не
вставлять значения паролей в аргументы команд, логи, чат или отчёты.
Production signing не должен fallback-иться на debug key.

## 15. PEPK

Если RuStore требует PEPK, использовать существующий production
keystore, alias и encryption key только со страницы RuStore, с
`--include-cert`. `.jks` в RuStore не загружать. Upload certificate
экспортировать `keytool -exportcert -rfc` в PEM.
PEPK ZIP и публичный upload certificate PEM — отдельные артефакты. Для
PEPK вводить пароли интерактивно; не передавать их флагами командной
строки и не сохранять encryption key RuStore в шаблонах.
Безопасная форма команды (подставить текущий key только из RuStore для
этого приложения):

    java -jar pepk.jar --keystore "<release.jks>" --alias "<alias>" --output="<pepk_out.zip>" --encryptionkey=<RUSTORE_PROVIDED_KEY> --include-cert

## 16. RuStore permissions --- сначала проверка

Если RuStore обнаружил sensitive permissions, не оправдывать сразу.
Проверить final release merged manifest и источник.

Реальные грабли: - `RECORD_AUDIO` может прийти от `expo-image-picker`; -
`SYSTEM_ALERT_WINDOW` --- Expo/RN/debug overlay; -
`READ/WRITE_EXTERNAL_STORAGE` --- legacy picker/filesystem.

Ненужные permissions удалять через config plugin / `blockedPermissions`
/ manifest merge. Если видео/микрофон не нужны --- отключать microphone
permission. Для современных Android использовать Photo Picker / SAF /
app-private storage.

После изменения: tests/typecheck/lint → `bundleRelease` тем же keystore
→ signing check → новый AAB SHA256.

## 17. RuStore и ложный AdMob

Автоопределение сети объявлений в RuStore может быть вызвано транзитивным
адаптером или analytics SDK. Проверять release dependency tree, manifest,
native dependencies, application IDs/config и FINAL AAB; карточка RuStore
сама по себе не доказывает наличие serving SDK.

Полный AdMob SDK: `play-services-ads`, `play-services-ads-lite`,
`play-services-ads-base`, mediation adapters.

ads-identifier, appset, AD_ID и revenue adapter без serving SDK не означают,
что приложение показывает рекламу этой сети. Отличать adapter, identifier,
metadata и полный ads SDK по resolved dependency set и AAB. Перед удалением
зависимости определить её фактическую роль.

## 18. RuStore: данные

Не отмечать данные, которые приложение фактически не использует. Если
используются только фото --- не оставлять «Видео» из-за возможностей
picker.

Обоснование permissions описывает только фактическое использование:
POST_NOTIFICATIONS — опциональные локальные reminders после opt-in;
RECEIVE_BOOT_COMPLETED — восстановление reminder после reboot; AD_ID —
только если реально используется ads/attribution SDK.

## 19. Privacy

Privacy соответствует реальному приложению. GitHub Pages подходит. Сайт
разработчика: `https://forest-music.ru`. Перед модерацией privacy URL
должен отвечать HTTP 200.

## 20. Final QA

Перед release проверить tests/typecheck/lint, native build/install/launch,
один Metro с подтверждённой project identity, SQLite regressions, active
timers, forms/keyboard, dark-mode smoke, Android Back, release
permissions/dependencies, Ads/AppMetrica init, notifications/image/document
picker, launcher icon, privacy HTTP 200, icon 512×512 и screenshots 1080×1920
без ads/debug UI. Нижние CTA проверять на физическом Android с
navigation/gesture bar.

После изменения autosave, restore или session identity физически проверить
ввод: выбрать cell/control → ввести значение → увидеть изменившееся состояние
→ проверить сохранение. Для таймера, исключающего background time: примерно
10 секунд active + 20 секунд background + 5 секунд active должны прибавить
около 15, а не 35 секунд. Отчёт явно разделяет automated, physical и
not-verified результаты.

## 21. После загрузки в RuStore

Если версия отправлена на модерацию --- не пересобирать без причины. При
замечании сначала точная причина, затем минимальное исправление. Новый
бинарник до публикации --- тот же keystore, signing verification и новый
AAB SHA256.

## 22. Главное правило

Не лечить проблему новым Metro, портом, AVD, каталогом, библиотекой или
signing key, пока не доказано, что существующий путь --- причина.
Сначала root cause, потом минимальное изменение.

# ANDROID QA / DEVICE STARTUP PLAYBOOK

Этот раздел обязателен для всех ForestMusic Android-проектов на React Native / Expo.

Цель:
- исключить повторяющиеся проблемы с Android SDK;
- исключить конфликты Metro 8081/8082/8083;
- не использовать случайный IPv6-only Metro;
- не запускать тяжёлый AVD без необходимости;
- не пересобирать APK без необходимости;
- всегда сначала диагностировать конкретную ошибку, а не выполнять destructive cleanup.

--------------------------------------------------
1. ПРИОРИТЕТ УСТРОЙСТВ
--------------------------------------------------

Основной путь QA:

1. Реальное Android-устройство, если подключено.
2. ForestMusic_Fast_API35 — основной AVD для обычной проверки.
3. Pixel_10 API 37 — только для редкой native/API-specific QA.

Если реальное устройство доступно через adb:
- НЕ запускать AVD без явной необходимости.

Не запускать тяжёлый Pixel_10 API 37 для обычных проверок.

--------------------------------------------------
2. PRE-FLIGHT: GIT
--------------------------------------------------

Перед Android-запуском из корня проекта:

git status
git pull
git rev-parse --short HEAD

Не выполнять npm install/npm ci автоматически при каждом запуске.

npm ci выполнять, если:
- node_modules отсутствует;
- изменился package-lock.json;
- зависимости явно требуют синхронизации.

Не изменять рабочее дерево без необходимости.

--------------------------------------------------
3. PRE-FLIGHT: ANDROID SDK
--------------------------------------------------

До запуска Gradle обязательно проверить Android SDK.

PowerShell:

Test-Path "$env:LOCALAPPDATA\Android\Sdk"
Test-Path ".\android\local.properties"

Стандартное расположение SDK на локальном Windows-ПК:

$env:LOCALAPPDATA\Android\Sdk

Если SDK существует, но android/local.properties отсутствует, создать:

"sdk.dir=$($env:LOCALAPPDATA.Replace('\','\\'))\\Android\\Sdk" |
    Set-Content .\android\local.properties

Проверить:

Get-Content .\android\local.properties

Ожидаемый вид:

sdk.dir=C:\\Users\\<USER>\\AppData\\Local\\Android\\Sdk

ВАЖНО:
- local.properties является локальной конфигурацией;
- НЕ добавлять local.properties в Git;
- НЕ запускать gradlew init;
- отсутствие ANDROID_HOME не является проблемой, если корректно задан sdk.dir.

--------------------------------------------------
4. PRE-FLIGHT: ADB
--------------------------------------------------

До запуска Expo/Gradle:

adb kill-server
adb start-server
adb devices

Если устройство имеет статус:

device

оно готово.

Если:

unauthorized

нужно подтвердить USB debugging на телефоне.

Если подключено реальное устройство:
- использовать его;
- AVD не запускать.

--------------------------------------------------
5. METRO: ЕДИНЫЙ СТАНДАРТ
--------------------------------------------------

Стандарт ForestMusic:

ONE PROJECT = ONE METRO = TCP 8081

Перед запуском проверить:

Get-NetTCPConnection -State Listen |
    Where-Object LocalPort -in 8081,8082,8083 |
    Format-Table LocalAddress,LocalPort,OwningProcess

Если обнаружены старые Metro на 8081/8082/8083:
- определить процесс;
- не запускать ещё один Metro автоматически;
- закрыть только подтверждённый ненужный Metro.

Проверка процессов:

Get-Process -Id (Get-NetTCPConnection -State Listen |
    Where-Object LocalPort -in 8081,8082,8083).OwningProcess -ErrorAction SilentlyContinue

Не позволять Expo молча переходить на 8082/8083.

Для данного QA-сеанса использовать строго:

8081

--------------------------------------------------
6. REAL DEVICE: ADB REVERSE
--------------------------------------------------

Для реального Android-устройства перед запуском Metro:

adb reverse tcp:8081 tcp:8081
adb reverse --list

Это стандартный способ связи устройства с локальным Metro.

Предпочитать adb reverse вместо ручной работы с:
- IP компьютера;
- Wi-Fi адресами;
- IPv6;
- сменой LAN IP.

Для обычного USB QA Metro должен быть доступен через localhost/ADB reverse.

--------------------------------------------------
7. ЗАПУСК METRO
--------------------------------------------------

Metro запускать отдельно и явно.

PowerShell, окно №1:

cd D:\petProject\<PROJECT>

$env:NODE_ENV="development"

npx expo start --dev-client --host lan --port 8081

Это окно оставить открытым.

Если есть доказанное подозрение на stale Metro/Metro cache:

npx expo start --dev-client --host lan --port 8081 --clear

Не использовать --clear автоматически при каждом запуске.

--------------------------------------------------
8. ЗАПУСК ANDROID
--------------------------------------------------

PowerShell, окно №2:

cd D:\petProject\<PROJECT>

adb devices
adb reverse --list

Затем:

npx expo run:android --port 8081

Не позволять Expo автоматически выбирать 8082/8083.

После запуска убедиться, что приложение использует именно текущий Metro текущего проекта.

--------------------------------------------------
9. ЕСЛИ APK УЖЕ СОБРАН
--------------------------------------------------

Не запускать повторную Gradle/Expo сборку без необходимости.

Для существующего debug APK:

adb install -r .\android\app\build\outputs\apk\debug\app-debug.apk

После установки приложение можно запускать непосредственно через adb:

adb shell am start -n <PACKAGE>/.MainActivity

При необходимости перед чистым runtime-тестом:

adb shell am force-stop <PACKAGE>

--------------------------------------------------
10. ЕСЛИ EXPO RUN:ANDROID / GRADLE УПАЛ
--------------------------------------------------

НЕ выполнять автоматически:

- gradlew clean;
- удаление node_modules;
- удаление android;
- expo prebuild --clean;
- npm install;
- npm ci;
- очистку всех Gradle caches;
- пересоздание проекта.

Сначала получить настоящую причину ошибки.

Из корня проекта:

cd .\android

.\gradlew.bat app:assembleDebug `
    --stacktrace `
    --console=plain 2>&1 |
    Tee-Object ..\gradle-error.txt

Вернуться:

cd ..

Извлечь значимые ошибки:

Select-String -Path .\gradle-error.txt `
    -Pattern "What went wrong","Execution failed","FAILURE:","error:","Caused by:" `
    -Context 3,15

Исправлять только установленную причину.

ВАЖНО:
Gradle wrapper должен запускаться из android directory либо с корректным project-dir.

Не выполнять gradlew init для существующего Android-проекта.

--------------------------------------------------
11. AVD FALLBACK
--------------------------------------------------

AVD используется только если:
- реального устройства нет;
- конкретная проверка требует эмулятора.

Основной AVD:

ForestMusic_Fast_API35

После запуска AVD обязательно:

adb devices

Продолжать только после появления emulator со статусом:

device

Pixel_10 API 37 использовать только для:
- редких native QA;
- API-specific проверки;
- случаев, где API 37 действительно необходим.

Не использовать Pixel_10 API 37 как стандартный AVD.

--------------------------------------------------
12. RUNTIME LOGCAT
--------------------------------------------------

Для проверки конкретного приложения не использовать общий logcat, если на устройстве другие SDK/приложения создают шум.

Получить PID:

adb shell pidof <PACKAGE>

Для текущего процесса:

$pidApp = (adb shell pidof <PACKAGE>).Trim()

adb logcat --pid=$pidApp -v time

Для чистой проверки:

adb shell am force-stop <PACKAGE>
adb logcat -c
adb shell am start -n <PACKAGE>/.MainActivity
Start-Sleep -Seconds 3
$pidApp = (adb shell pidof <PACKAGE>).Trim()
adb logcat --pid=$pidApp -d -v time

Для AppMetrica:

adb logcat --pid=$pidApp -d -v time |
    Select-String -Pattern "AppMetrica|appmetrica|ReactNativeJS"

Не делать выводы по AppMetrica из общего logcat без PID-фильтрации.

--------------------------------------------------
13. DEBUG APK VS RELEASE
--------------------------------------------------

Всегда различать:

DEBUG QA:
- может использовать Metro;
- JS может загружаться с dev server;
- возможен stale/wrong Metro;
- наличие нового APK само по себе не доказывает, что выполняется нужный JS bundle.

RELEASE QA:
- JS bundle встроен;
- Metro не должен требоваться;
- ближе к фактической RuStore-сборке.

Если debug runtime показывает поведение, не соответствующее текущему source:
проверить Metro/порт/cache до изменения кода.

--------------------------------------------------
14. PRODUCTION AAB
--------------------------------------------------

Production AAB НЕ собирать просто для проверки UI/runtime, если достаточно debug QA.

Финальный AAB собирать только после:
- source checks PASS;
- tests PASS;
- Android compile PASS;
- real-device QA PASS;
- критические SDK/analytics/ads проверки PASS.

После сборки production AAB обязательно проверить:
- package;
- versionName;
- versionCode;
- signing certificate;
- bundletool validate;
- необходимые native SDK;
- SHA-256;
- отсутствие signing secrets в Git.

--------------------------------------------------
15. ЗАПРЕЩЁННЫЙ ПАТТЕРН ДИАГНОСТИКИ
--------------------------------------------------

При первой Android-ошибке НЕ делать хаотически:

clean
→ npm install
→ удалить node_modules
→ prebuild
→ сменить порт
→ запустить другой AVD
→ перезагрузить adb
→ пересобрать всё.

Правильный порядок:

PRE-FLIGHT
→ определить конкретный failing layer
→ получить точную ошибку
→ минимальное исправление
→ повторить только упавший шаг.

Слои проверяются по порядку:

1. Git/source
2. Android SDK
3. ADB/device
4. Metro/8081
5. adb reverse
6. Gradle compile
7. APK install
8. runtime
9. PID-filtered logcat
10. application-specific QA

--------------------------------------------------
16. ЦЕЛЕВОЙ БЫСТРЫЙ СЦЕНАРИЙ
--------------------------------------------------

Для обычного QA на подключённом реальном телефоне после первоначальной настройки проекта стандартный цикл должен сводиться к:

Terminal 1:

cd D:\petProject\<PROJECT>
git pull
adb devices
adb reverse tcp:8081 tcp:8081
npx expo start --dev-client --host lan --port 8081

Terminal 2:

cd D:\petProject\<PROJECT>
npx expo run:android --port 8081

Если APK уже актуален:

adb install -r .\android\app\build\outputs\apk\debug\app-debug.apk

Никаких дополнительных действий не выполнять без конкретной причины.

--------------------------------------------------
17. AUTOMATION GOAL
--------------------------------------------------

Желательно иметь в каждом ForestMusic RN/Expo проекте:

scripts/android-qa.ps1

Скрипт должен автоматизировать безопасные pre-flight проверки:

- Git status;
- Android SDK existence;
- local.properties existence;
- adb availability;
- connected devices;
- Metro ports 8081/8082/8083;
- adb reverse;
- предупреждение о конфликтующем Metro;
- выбор real device прежде AVD.

Скрипт НЕ должен автоматически:
- убивать неизвестные процессы;
- удалять caches;
- делать gradlew clean;
- удалять node_modules;
- выполнять prebuild --clean;
- запускать тяжёлый AVD;
- изменять tracked source files.

Все destructive/recovery actions выполняются только после установленной причины.

# FORESTMUSIC — REAL ANDROID DEVICE STARTUP / QA PLAYBOOK

Этот раздел является стандартным способом запуска всех React Native / Expo
ForestMusic-приложений на реальном Android-устройстве.

ЦЕЛЬ:
запуск приложения на телефоне должен занимать несколько минут, а не превращаться
в повторную диагностику SDK / Gradle / Metro / IPv4 / ADB / старого APK.

ОСНОВНОЙ ПРИНЦИП:

REAL DEVICE
→ SDK CHECK
→ ADB
→ BUILD
→ INSTALL
→ VERIFY INSTALLED VERSION
→ ONE METRO :8081
→ ADB REVERSE
→ DEV CLIENT VIA 127.0.0.1
→ APP VISIBLE
→ PID-FILTERED LOGCAT

Не менять порядок без конкретной причины.

======================================================================
1. REAL DEVICE — ПРИОРИТЕТ
======================================================================

Для обычного QA:

1. Реальный Android-телефон — основной вариант.
2. ForestMusic_Fast_API35 — fallback.
3. Pixel_10 API37 — только редкие native/API-specific проверки.

Если:

adb devices

показывает реальный телефон со статусом:

device

AVD НЕ запускать.

======================================================================
2. ПЕРЕД ЗАПУСКОМ — GIT
======================================================================

Из корня проекта:

git status
git pull --ff-only origin main
git rev-parse HEAD

Убедиться, что QA проводится на ожидаемом SHA.

Не запускать npm install/npm ci без причины.

======================================================================
3. ANDROID SDK — ПРОВЕРИТЬ ДО GRADLE
======================================================================

PowerShell:

Test-Path "$env:LOCALAPPDATA\Android\Sdk"
Test-Path ".\android\local.properties"

Если Android SDK существует, но local.properties отсутствует:

"sdk.dir=$($env:LOCALAPPDATA.Replace('\','\\'))\\Android\\Sdk" |
    Set-Content .\android\local.properties

Проверить:

Get-Content .\android\local.properties

local.properties:
- локальный файл;
- НЕ добавлять в Git.

ANDROID_HOME может быть пустым, если sdk.dir корректно задан.

НИКОГДА не выполнять gradlew init для существующего Android-проекта.

======================================================================
4. ПРОВЕРИТЬ ТЕЛЕФОН
======================================================================

adb kill-server
adb start-server
adb devices -l

Ожидается:

<DEVICE_SERIAL>    device ...

Если:
unauthorized

→ подтвердить USB debugging на телефоне.

Если устройства нет:
→ сначала исправить USB/ADB.
Не диагностировать Metro или приложение.

======================================================================
5. BUILD DEBUG APK
======================================================================

Для чистого device QA предпочтительно собирать Gradle напрямую:

cd android
.\gradlew.bat assembleDebug --console=plain
cd ..

Ожидается:

BUILD SUCCESSFUL

Не считать compileDebug достаточным:
для установки нужен фактически собранный APK.

Стандартный APK:

android\app\build\outputs\apk\debug\app-debug.apk

======================================================================
6. ПРОВЕРИТЬ СВЕЖЕСТЬ APK
======================================================================

После сборки:

Get-Item .\android\app\build\outputs\apk\debug\app-debug.apk |
    Format-List FullName,Length,LastWriteTime

APK должен иметь актуальное время сборки.

Нельзя проводить QA на старом APK только потому, что файл уже существует.

======================================================================
7. ОБЯЗАТЕЛЬНО УСТАНОВИТЬ СВЕЖИЙ APK
======================================================================

assembleDebug НЕ устанавливает APK на телефон.

После каждой новой сборки:

adb install -r .\android\app\build\outputs\apk\debug\app-debug.apk

Ожидается:

Success

Если нужно гарантированно исключить старое состояние:

adb uninstall <PACKAGE>
adb install .\android\app\build\outputs\apk\debug\app-debug.apk

Использовать uninstall только когда действительно нужен clean install.

======================================================================
8. ОБЯЗАТЕЛЬНО ПРОВЕРИТЬ ФАКТИЧЕСКИ УСТАНОВЛЕННУЮ ВЕРСИЮ
======================================================================

Сразу после установки:

adb shell dumpsys package <PACKAGE> |
    Select-String "versionName|versionCode"

QA НЕ ПРОДОЛЖАТЬ, пока фактически установленная версия не совпадает
с ожидаемой.

Пример:

versionCode=2
versionName=1.0.1

ВАЖНО:

app.json != доказательство установленной версии.
build.gradle != доказательство установленной версии.
успешный assembleDebug != доказательство установленной версии.

Источник истины перед device QA:

dumpsys установленного package.

======================================================================
9. VERSION CONSISTENCY
======================================================================

Для Expo prebuild/native проектов проверить:

npx expo config --type public |
    Select-String -Pattern "version|versionCode" -Context 1,1

и:

Select-String -Path .\android\app\build.gradle `
    -Pattern "versionCode|versionName"

Если Expo config и android/app/build.gradle расходятся:
STOP.

Не использовать expo prebuild --clean для простого исправления версии.

Исправить конкретный источник версии и иметь regression test,
который блокирует подобное расхождение.

======================================================================
10. METRO — ВСЕГДА ОДИН И ТОЛЬКО 8081
======================================================================

Стандарт:

ONE PROJECT = ONE METRO = PORT 8081

Перед запуском Metro:

Get-NetTCPConnection -State Listen -ErrorAction SilentlyContinue |
    Where-Object LocalPort -in 8081,8082,8083 |
    Format-Table LocalAddress,LocalPort,OwningProcess

Если 8081 занят:

НЕ соглашаться на:

Use port 8082 instead? — NO. Keep the single-project port 8081 and diagnose
the owner of the existing listener.

Ответ:

n

Сначала определить владельца 8081:

$metroPid = (Get-NetTCPConnection -State Listen -LocalPort 8081).OwningProcess

Get-Process -Id $metroPid |
    Format-List Id,ProcessName,Path

Если подтверждено, что это старый node/Metro:

Stop-Process -Id $metroPid -Force

Не убивать неизвестный процесс автоматически.

После остановки:

Test-NetConnection 127.0.0.1 -Port 8081

До запуска нового Metro ожидается:

TcpTestSucceeded : False

======================================================================
11. ЗАПУСК METRO
======================================================================

Отдельное окно PowerShell:

cd D:\petProject\<PROJECT>

$env:NODE_ENV="development"

npx expo start --dev-client --host lan --port 8081

Окно оставить открытым.

НЕ использовать автоматически 8082/8083.

======================================================================
12. "::" НА WINDOWS НЕ ОЗНАЧАЕТ, ЧТО IPv4 СЛОМАН
======================================================================

Get-NetTCPConnection может показать:

LocalAddress ::
LocalPort    8081

НЕ считать это автоматической ошибкой.

Windows socket может быть dual-stack.

Реальная проверка IPv4:

Test-NetConnection 127.0.0.1 -Port 8081

Если:

TcpTestSucceeded : True

IPv4 localhost работает.

Не перезапускать Metro только ради появления:

0.0.0.0

======================================================================
13. ADB REVERSE — ОБЯЗАТЕЛЬНО ДЛЯ USB QA
======================================================================

При работающем Metro:

adb reverse --remove-all
adb reverse tcp:8081 tcp:8081
adb reverse --list

Ожидается:

tcp:8081 tcp:8081

Для USB QA это стандартный маршрут.

Не использовать LAN IP без необходимости.

======================================================================
14. НЕ НАЖИМАТЬ "a" ДЛЯ НАШЕГО СТАНДАРТНОГО USB QA
======================================================================

Expo может открыть development client с LAN URL вида:

192.168.x.x:8081

Это может привести к:

failed to connect 192.168.x.x:8081

Хотя adb reverse настроен правильно.

Поэтому при USB QA приложение открывать ЯВНО через localhost.

Стандартная команда:

adb shell am start `
  -a android.intent.action.VIEW `
  -d "exp+<EXPO-SCHEME>://expo-development-client/?url=http%3A%2F%2F127.0.0.1%3A8081"

Например:

exp+shift-calendar://expo-development-client/?url=http%3A%2F%2F127.0.0.1%3A8081

Использовать scheme конкретного проекта.

======================================================================
15. DEVELOPMENT BUILD != ПРИЛОЖЕНИЕ ЗАПУЩЕНО
======================================================================

Если на телефоне виден только экран:

Development Build

это НЕ означает, что приложение работает.

QA начинается только после того, как development client:
- подключился к Metro;
- загрузил JS bundle;
- показал настоящий UI приложения.

Нужно визуально увидеть приложение.

При необходимости подтвердить:

adb logcat ...

и наличие:

ReactNativeJS: Running "main"

======================================================================
16. METRO CACHE CORRUPTION
======================================================================

Если Metro пишет:

Unable to deserialize cloned data

или:

Error while reading cache, falling back to a full crawl

это подтверждённая проблема Metro cache.

Тогда разрешено:

Ctrl+C

и:

npx expo start --dev-client --host lan --port 8081 --clear

НЕ делать при этом:

- удаление node_modules;
- npm install;
- Gradle clean;
- удаление android;
- expo prebuild --clean.

--clear использовать только при доказанной проблеме Metro cache,
а не при каждом запуске.

======================================================================
17. ЕСЛИ ПРИЛОЖЕНИЕ ПОКАЗЫВАЕТ JS ERROR
======================================================================

Если Dev Launcher показывает JS/Syntax error:

НЕ пересобирать Android сразу.

Сначала проверить bundle:

npx expo export --platform android --output-dir tmp\bundle-check

Если export FAIL:
→ исправить конкретный JS/TS/bundle error.

Если export PASS:
→ проблема не является обычной compile-time syntax error;
проверять Dev Launcher/runtime.

======================================================================
18. LOGCAT — ТОЛЬКО ПО PID ПРИ ДИАГНОСТИКЕ ПРИЛОЖЕНИЯ
======================================================================

Общий logcat может содержать:
- другие приложения;
- Yandex Ads;
- внутренние AppMetrica reporters;
- системный шум.

Получить PID:

$pidApp = (adb shell pidof <PACKAGE>).Trim()

Затем:

adb logcat --pid=$pidApp -v time

Для snapshot:

adb logcat --pid=$pidApp -d -v time

Для чистого запуска:

adb shell am force-stop <PACKAGE>
adb logcat -c

<OPEN APP VIA 127.0.0.1 DEV CLIENT>

Start-Sleep -Seconds 5

$pidApp = (adb shell pidof <PACKAGE>).Trim()

adb logcat --pid=$pidApp -d -v time

======================================================================
19. APPMETRICA QA
======================================================================

Для AppMetrica:

adb logcat --pid=$pidApp -d -v time |
    Select-String -Pattern "AppMetrica|appmetrica|ReactNativeJS"

PASS подтверждается не просто наличием слова AppMetrica,
а нашим package/API key.

Пример:

[com.calculatorplatform.shiftcalendar]
[e0b1b59c-xxxx-xxxx-xxxx-xxxxxxxx6a32]

Resume session

и/или:

Event received on service: EVENT_TYPE_START

Внутренние reporters Yandex Ads не считать нашей AppMetrica activation.

======================================================================
20. НЕ ПУТАТЬ DEBUG И RELEASE
======================================================================

DEBUG:
- требует Metro для JS runtime;
- может использовать development client;
- Metro может быть stale/wrong;
- adb reverse необходим для нашего стандартного USB workflow.

RELEASE:
- JS bundle embedded;
- Metro не требуется;
- localhost не требуется;
- development client не требуется.

Production AAB никогда не должен зависеть от Metro.

======================================================================
21. ПРИ ПАДЕНИИ GRADLE — СНАЧАЛА ПРИЧИНА
======================================================================

Не делать сразу clean.

Из android:

.\gradlew.bat app:assembleDebug `
    --stacktrace `
    --console=plain 2>&1 |
    Tee-Object ..\gradle-error.txt

Затем из корня:

Select-String -Path .\gradle-error.txt `
    -Pattern "What went wrong","Execution failed","FAILURE:","error:","Caused by:" `
    -Context 3,15

Исправить конкретную причину.

======================================================================
22. ЗАПРЕЩЁННЫЙ RECOVERY PATTERN
======================================================================

НЕ делать хаотически:

gradlew clean
→ удалить node_modules
→ npm install
→ prebuild --clean
→ сменить 8081 на 8082
→ запустить другой AVD
→ пересобрать всё.

Правильно:

SOURCE
→ SDK
→ DEVICE
→ BUILD
→ INSTALL
→ DUMPSYS
→ METRO 8081
→ IPV4 TEST
→ ADB REVERSE
→ 127.0.0.1 DEV CLIENT
→ REAL APP UI
→ PID LOGCAT

======================================================================
23. СТАНДАРТНЫЙ БЫСТРЫЙ ЗАПУСК
======================================================================

После того как проект уже один раз настроен, обычный запуск должен выглядеть так.

-------------------------
WINDOW 1 — BUILD / DEVICE
-------------------------

cd D:\petProject\<PROJECT>

git pull --ff-only origin main

adb devices -l

cd android
.\gradlew.bat assembleDebug --console=plain
cd ..

adb install -r .\android\app\build\outputs\apk\debug\app-debug.apk

adb shell dumpsys package <PACKAGE> |
    Select-String "versionName|versionCode"

-------------------------
WINDOW 2 — METRO
-------------------------

cd D:\petProject\<PROJECT>

$env:NODE_ENV="development"

npx expo start --dev-client --host lan --port 8081

-------------------------
WINDOW 1 — CONNECTION
-------------------------

Test-NetConnection 127.0.0.1 -Port 8081

adb reverse --remove-all
adb reverse tcp:8081 tcp:8081
adb reverse --list

adb shell am start `
  -a android.intent.action.VIEW `
  -d "exp+<EXPO-SCHEME>://expo-development-client/?url=http%3A%2F%2F127.0.0.1%3A8081"

-------------------------
PASS CRITERIA
-------------------------

PASS только если:

1. BUILD SUCCESSFUL
2. adb install = Success
3. dumpsys показывает ожидаемую versionName/versionCode
4. 127.0.0.1:8081 = TcpTestSucceeded True
5. adb reverse показывает tcp:8081 tcp:8081
6. на телефоне отображается настоящий UI приложения
7. при необходимости ReactNativeJS показывает Running "main"

======================================================================
24. АВТОМАТИЗАЦИЯ — ОБЯЗАТЕЛЬНАЯ ЦЕЛЬ
======================================================================

Для ForestMusic RN/Expo проектов создать единый:

scripts/android/android-device-qa.ps1

Он должен безопасно автоматизировать:

- проверку Android SDK;
- создание local.properties при отсутствии;
- adb devices;
- проверку ожидаемого package;
- проверку 8081;
- определение владельца занятого 8081;
- предупреждение о старом Metro;
- запуск Metro строго 8081;
- Test-NetConnection 127.0.0.1:8081;
- adb reverse;
- assembleDebug;
- install APK;
- dumpsys version;
- открытие dev client через 127.0.0.1;
- PID lookup;
- удобный PID-filtered logcat.

Скрипт НЕ должен автоматически:
- убивать неизвестный процесс;
- делать gradlew clean;
- удалять node_modules;
- делать prebuild --clean;
- менять source;
- менять version;
- запускать тяжёлый AVD.

После обкатки скрипта на одном проекте использовать одинаковый шаблон
во всех ForestMusic RN/Expo проектах.
