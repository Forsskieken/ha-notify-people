# ha-notify-people

A Home Assistant script that sends a message to people **by name**: to their phone (companion app) and to Home Assistant's own notification list.

Automations write `recipients: [alice]` instead of `notify.mobile_app_…`. Get a new phone, and you change one line instead of every automation.

## What it does

| Field | Required | What |
|---|---|---|
| `recipients` | yes | List of names, e.g. `[alice, bob]` |
| `title` | no | Title of the message |
| `message` | yes | The text |
| `channel` | no | Android notification channel, so a channel can have its own sound |
| `priority` | no | `high` wakes the phone immediately; default `normal` |
| `extra` | no | Other [companion-app data](https://companion.home-assistant.io/docs/notifications/notifications-basic), e.g. `{ttl: 0}`, `clickAction`, `actions` |
| `persistent` | no | `false` = phone only, nothing in the notification list (default `true`) |

- An unknown name gives a warning in the Home Assistant log; the other recipients still get the message.
- One phone failing does not stop the message to the others.

## Requirements

- Home Assistant 2024.8 or newer.
- The [companion app](https://companion.home-assistant.io/) on each phone, logged in to your Home Assistant.

## Install

1. **Find each phone's action name.** *Developer tools → Actions*, type `notify.mobile_app_`: one action per phone, e.g. `notify.mobile_app_pixel_8`.
2. **Edit [`notify_people.yaml`](notify_people.yaml).** Under `people:`, replace `alice`/`bob` and their actions with your names and actions. Put the same names under `options:` (near the top).
3. **Add the script.** *Settings → Automations & scenes → Scripts → + Create script → Create new script*, then *⋮ → Edit in YAML*. Replace everything with the edited file and save.
4. **Check the name.** The script must be `script.notify_people` (*⋮ → Settings → Entity ID*); rename it if Home Assistant chose something else.

Using `scripts.yaml` instead of the UI? Add the file's contents under a key `notify_people:`, indented two spaces, then *Developer tools → YAML → Scripts*.

## Test

*Developer tools → Actions*, choose **Notify people**, switch to YAML mode and run:

```yaml
action: script.notify_people
data:
  recipients: [alice]
  title: Test
  message: Hello from Home Assistant
```

The message shows up on the phone and under the bell icon in Home Assistant's sidebar.

## Use in an automation

```yaml
actions:
  - action: script.notify_people
    data:
      recipients: [alice, bob]
      title: "Basement: water detected"
      message: "Sensor {{ trigger.to_state.name }} is wet."
      priority: high
```

Open a web page when the message is tapped:

```yaml
      extra:
        clickAction: https://www.example.com/
```

## License

MIT, see [LICENSE](LICENSE).
