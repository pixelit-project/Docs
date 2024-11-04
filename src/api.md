---
collapsable: false
sidebarDepth: 2
---

# API

You can use the API via the HTTP REST Interface or MQTT. The HTTP API is available at `/api/` and the MQTT API at `<MasterTopic>/`. You can use both APIs at the same time.

::: tip
If you use the [Node-RED](nodered.html) or [ioBroker](iobroker.html) integration, you don't need to use the API directly.
:::

## Interfaces

### REST

The port is `80`, The REST API is available at `/api/`. The following endpoints are available:

### MQTT

The default MasterTopic is `pixelit/`, but can be changed in the WebUI. The MasterTopic is used as a prefix for all MQTT topics. If enabled, the device also uses an additional topic with the name `<MasterTopic>/<hostname>`. When you use MQTT, you are able to use multiple PixelIts but with the additional topic you can also address a single PixelIt.

With MQTT enabled, the device will also discoverable via [Home Assistant](https://www.home-assistant.io/integrations/mqtt/).

::: tip
We recommend using MQTT.
:::

## Endpoints

The following endpoints are available:

| Endpoint       | Description                      | Details                               |
| :------------- | :------------------------------- | ------------------------------------- |
| `screen`       | Show screen on device            | [Details](api.html#screen)            |
| `brightness`   | Get or set brightness            | [Details](api.html#brightness)        |
| `buttons`      | Get button states                | [Details](api.html#buttons)           |
| `luxsensor`    | Get lux sensor value             | [Details](api.html#lux-sensor)        |
| `sensor`       | Get sensor values                | [Details](api.html#sensor)            |
| `state`        | Get state (MQTT only)            | [Details](api.html#state)             |
| `matrixinfo`   | Get matrix info                  | [Details](api.html#matrix-infomation) |
| `config`       | Get or set config                | [Details](api.html#matrix-config)     |
| `wifireset`    | WiFi reset                       | [Details](api.html#matrix-config)     |
| `factoryreset` | Reset device to factory defaults | [Details](api.html#matrix-config)     |

## Screen

Most important endpoint is `/api/screen` or `<MasterTopic>/setScreen`. This endpoint is used to display a single screen on Pixelit. The following elements are supported:

::: blue HTTP Endpoint [POST]
/api/screen
:::
::: blue MQTT Topic [PUBLISH]
&lt;MasterTopic&gt;/setScreen
:::

You **must** combine all elements in one call that are to be displayed as one screen.

Example call:
::: details Example call:

#### Calling the internal clock with a fade animation:

```json
{
  "switchAnimation": {
    "aktiv": true,
    "animation": "fade"
  },
  "clock": {
    "show": true,
    "switchAktiv": true,
    "withSeconds": false,
    "switchSec": 6,
    "hexColor": "#1E00FF"
  }
}
```

#### Call fade animation, image and text:

```json
{
  "switchAnimation": {
    "aktiv": true,
    "animation": "fade"
  },
  "bitmap": {
    "data": [
      0, 0, 0, 21855, 0, 0, 0, 0, 0, 0, 0, 21855, 0, 0, 0, 0, 0, 0, 21855,
      21855, 21855, 0, 0, 0, 0, 0, 21855, 21855, 21855, 0, 0, 0, 0, 693, 1055,
      1055, 65535, 44735, 0, 0, 0, 693, 1055, 65535, 65535, 44735, 0, 0, 0, 693,
      1055, 1055, 65535, 21855, 0, 0, 0, 0, 693, 1055, 21855, 0, 0, 0
    ],
    "position": {
      "x": 0,
      "y": 0
    },
    "size": {
      "width": 8,
      "height": 8
    }
  },
  "text": {
    "textString": "59%",
    "bigFont": false,
    "scrollText": false,
    "scrollTextDelay": 0,
    "centerText": true,
    "position": {
      "x": 7,
      "y": 1
    },
    "hexColor": "#1E00FF"
  }
}
```

:::

### Text

---

The following special characters are supported:
`€ ← ↑ → ↓ ★ ♥ ↧ 🚗 😀 📁 📄`
![](/special_characters.png)

```json
{
  "text": {
    "textString": "Test It :D",
    "bigFont": false,
    "scrollText": "auto",
    "scrollTextDelay": 20,
    "centerText": false,
    "position": {
      "x": 8,
      "y": 1
    },
    "color": {
      "r": 255,
      "g": 255,
      "b": 255
    },
    "hexColor": "#FFFFFF"
  }
}
```

| Parameter         | Type               | Values                         | Description                             |
| :---------------- | :----------------- | :----------------------------- | :-------------------------------------- |
| `textString`      | `String`           |                                | **Required**. Displayed Text            |
| `scrollText`      | `Boolean / String` | `true / false / auto`          | **Required** Big front                  |
| `scrollTextDelay` | `Integer`          | `1 - 9999`                     | **Required (when scrollText is used)**  |
| `centerText`      | `Boolean`          | `true / false`                 | **Required**                            |
| `position`        | `JSON`             | `{"x":8, "y":1}`               | **Required**                            |
| `color`           | `JSON`             | `{"r":255, "g":255, "b":255},` | **Required** (or hexColor)              |
| `hexColor`        | `String`           | `#FFFFFF`                      | **since v0.3.2** Alternatively to color |

### Sleep Mode

---

`sleepMode` could be send as a key of a screen payload. When `sleepMode` is active, the device dimms down the matrix to `brightness: 0`.
Additionally while `sleepMode` is active the `/api/screen`-endpoint and `<MasterTopic>/setScreen`-topic are not fully evaluated. Only when `sleepMode` previously or within the same message-body is set to `false` , the requested screen will be displayed.

:::warning Disclaimer
Enabeling `sleepMode` does not enable power saving features on the MCU itself, like e.g. deep sleep functionality of an ESP8266.
:::

```json
{
  "sleepMode": false
}
```

| Parameter   | Type      | Values         | Description                                                   |
| :---------- | :-------- | :------------- | :------------------------------------------------------------ |
| `sleepMode` | `Boolean` | `true / false` | When acitve `sleepMode` disables the rendering of new screens |

### Brightness

---

```json
{
  "brightness": 125
}
```

| Parameter    | Type      | Values    | Description         |
| :----------- | :-------- | :-------- | :------------------ |
| `brightness` | `Integer` | `0 - 255` | Sets the brightness |

### Switch Animation

---

```json
{
  "switchAnimation": {
    "aktiv": true,
    "animation": "fade"
  }
}
```

#### Call bitmapWipe example

```json
{
  "switchAnimation": {
    "aktiv": true,
    "animation": "fade",
    "data": [0, 0, 0, 0, 0, 0],
    "width": 8
  }
}
```

| Parameter   | Type            | Values                                                     | Description                                                 |
| :---------- | :-------------- | :--------------------------------------------------------- | :---------------------------------------------------------- |
| `aktiv`     | `Boolean`       | `true / false`                                             |                                                             |
| `animation` | `String`        | `fade / coloredBarWipe / zigzagWipe / bitmapWipe / random` | [bitmapWipe call example](api.html#call-bitmapwipe-example) |
| `data`      | `Integer Array` | `[0, 0, 0, 0, 0, 0]`                                       | **Required for bitmapWipe**                                 |
| `width`     | `Integer`       | `1 - 32`                                                   | **Required for bitmapWipe** height always needs to be 8     |

### Clock

---

```json
{
  "clock": {
    "show": true,
    "switchAktiv": true,
    "withSeconds": true,
    "switchSec": 5,
    "drawWeekDays": true,
    "color": {
      "r": 255,
      "g": 255,
      "b": 255
    },
    "hexColor": "#FFFFFF"
  }
}
```

| Parameter      | Type      | Values                         | Description                                                                |
| :------------- | :-------- | :----------------------------- | :------------------------------------------------------------------------- |
| `show`         | `Boolean` | `true / false`                 | **Required**                                                               |
| `switchAktiv`  | `Boolean` | `true / false`                 | Switch clock/date                                                          |
| `switchSec`    | `Integer` | `1 - 9999`                     | **Required (when Switch clock/date is used)** Switch clock/date in seconds |
| `drawWeekDays` | `Boolean` | `true / false`                 | **since v1.1.0** Draw weekdays                                             |
| `color`        | `JSON`    | `{"r":255, "g":255, "b":255},` | Color of Clock (or hexColor)                                               |
| `hexColor`     | `String`  | `#FFFFFF`                      | **since v0.3.2** Alternatively to color                                    |

### Bitmap

---

```json
{
  "bitmap": {
    "data": [0, 0, 0, 0, 0, 0],
    "position": {
      "x": 0,
      "y": 0
    },
    "size": {
      "width": 8,
      "height": 8
    }
  }
}
```

| Parameter  | Type            | Values                    | Description  |
| :--------- | :-------------- | :------------------------ | :----------- |
| `data`     | `Integer Array` | `[0, 0, 0, 0, 0, 0]`      | **Required** |
| `position` | `JSON`          | `{"x":0, "y":0}`          | **Required** |
| `size`     | `JSON`          | `{"width":8, "height":8}` | **Required** |

### Bitmaps

---

How to display multiple bitmaps on one screen.
::: warning Limitations
When displaying multiple bitmaps, animated bitmaps, scrolling or text are not supported!
:::

```json
{
  "bitmaps": [
    {
      "data": [0, 0, 0, 0, 0, 0],
      "position": {
        "x": 0,
        "y": 0
      },
      "size": {
        "width": 8,
        "height": 8
      }
    },
    {
      "data": [0, 0, 0, 0, 0, 0],
      "position": {
        "x": 8,
        "y": 0
      },
      "size": {
        "width": 8,
        "height": 8
      }
    },
    {
      "data": [0, 0, 0, 0, 0, 0],
      "position": {
        "x": 16,
        "y": 0
      },
      "size": {
        "width": 8,
        "height": 8
      }
    },
    {
      "data": [0, 0, 0, 0, 0, 0],
      "position": {
        "x": 24,
        "y": 0
      },
      "size": {
        "width": 8,
        "height": 8
      }
    }
  ]
}
```

| Parameter  | Type            | Values                    | Description  |
| :--------- | :-------------- | :------------------------ | :----------- |
| `data`     | `Integer Array` | `[0, 0, 0, 0, 0, 0]`      | **Required** |
| `position` | `JSON`          | `{"x":0, "y":0}`          | **Required** |
| `size`     | `JSON`          | `{"width":8, "height":8}` | **Required** |

### Bitmap Animation

---

```json
{
  "bitmapAnimation": {
    "data": [
      [0, 0, 0, 0, 0, 0],
      [0, 0, 0, 0, 0, 0],
      [0, 0, 0, 0, 0, 0]
    ],
    "animationDelay": 20,
    "rubberbanding": false,
    "limitLoops": 0
  }
}
```

| Parameter        | Type                      | Values                                    | Description                                    |
| :--------------- | :------------------------ | :---------------------------------------- | :--------------------------------------------- |
| `data`           | `Array of Integer Arrays` | `[[0, 0, 0, 0, 0, 0],[0, 0, 0, 0, 0, 0]]` | **Required** Only 8x8 BMPs are supported here! |
| `animationDelay` | `Integer`                 | `0 - 99999`                               | **Required** Higher is a slower animation      |
| `rubberbanding`  | `Boolean`                 | `true / false`                            | Should the animation run back and forth        |
| `limitLoops`     | `Integer`                 | `0-99`                                    | Limiting the loops                             |
| `position`       | `JSON`                    | `{"x":0, "y":0}`                          | Postion                                        |

### Bar

---

```json
{
  "bar": {
    "position": {
      "x": 0,
      "y": 0,
      "x2": 0,
      "y2": 0
    },
    "color": {
      "r": 255,
      "g": 255,
      "b": 255
    },
    "hexColor": "#FFFFFF"
  }
}
```

| Parameter  | Type     | Values                           | Description                             |
| :--------- | :------- | :------------------------------- | :-------------------------------------- |
| `position` | `JSON`   | `{"x":0, "y":0,"x2":1, "y2":10}` | **Required**                            |
| `color`    | `JSON`   | `{"r":255, "g":255, "b":255},`   | Color of Clock (or hexColor)            |
| `hexColor` | `String` | `#FFFFFF`                        | **since v0.3.2** Alternatively to color |

### Bars

---

Here you can pass an array of bars to draw several on one screen.

```json
{
  "bars": [
    {
      "position": {
        "x": 0,
        "y": 0,
        "x2": 0,
        "y2": 0
      },
      "color": {
        "r": 255,
        "g": 255,
        "b": 255
      },
      "hexColor": "#FFFFFF"
    }
  ]
}
```

| Parameter  | Type     | Values                           | Description                             |
| :--------- | :------- | :------------------------------- | :-------------------------------------- |
| `position` | `JSON`   | `{"x":0, "y":0,"x2":1, "y2":10}` | **Required**                            |
| `color`    | `JSON`   | `{"r":255, "g":255, "b":255},`   | Color of Clock (or hexColor)            |
| `hexColor` | `String` | `#FFFFFF`                        | **since v0.3.2** Alternatively to color |

### Set GPIO

```json
{
  "setGpio": {
    "gpio": 14,
    "set": true,
    "duration": 500
  }
}
```

| Parameter  | Type      | Values         | Description                                  |
| :--------- | :-------- | :------------- | :------------------------------------------- |
| `gpio`     | `Integer` | `E.g. 14`      | **Required** GPIO of the ESP                 |
| `set`      | `Boolean` | `true / false` | **Required**                                 |
| `duration` | `Integer` | `1 - 99999999` | Automatic fall back to false in milliseconds |

### Sound

---

#### Option 1

This option allows the use of multiple folders, but has the limitations, that the controls **next** and **previous** are omitted.

The following folders structure must be provided: `sd:\01\001.mp3`

```json
{
  "sound": {
    "volume": 20,
    "control": "play",
    "folder": 1,
    "file": 1
  }
}
```

| Parameter | Type      | Values         | Description  |
| :-------- | :-------- | :------------- | :----------- |
| `volume`  | `Integer` | `0 - 30`       | **Required** |
| `control` | `String`  | `play / pause` | **Required** |
| `folder`  | `Integer` | `1 - 99`       | **Required** |
| `file`    | `Integer` | `1 - 255`      | **Required** |

#### Option 2

This option also enables the controls **next** and **previous**, but the following order structure must be provided: `sd:\MP3\0001.mp3`

```json
{
  "sound": {
    "volume": 20,
    "control": "play",
    "file": 1
  }
}
```

| Parameter | Type      | Values                           | Description  |
| :-------- | :-------- | :------------------------------- | :----------- |
| `volume`  | `Integer` | `0 - 30`                         | **Required** |
| `control` | `String`  | `play / pause / next / previous` | **Required** |
| `file`    | `Integer` | `1 - 3000`                       | **Required** |

## Brightness

::: blue HTTP Endpoint [GET]
/api/brightness
:::  
This option can be used to query the current brightness. If u use MQTT, use `<MasterTopic>/matrixinfo` to get the current brightness.

The response has the following structure:

```json
{
  "brightness_255": 20,
  "brightness": 7
}
```

## Buttons

::: blue HTTP Endpoint [GET]
/api/buttons
:::

The HTTP response has the following structure:

```json
{
  "leftButton": "false",
  "middleButton": "true",
  "rightButton": "false"
}
```

::: blue MQTT Topic [SUBSCRIBE]
&lt;MasterTopic&gt;/buttons/#<br />
&lt;MasterTopic&gt;/buttons/button0<br />
&lt;MasterTopic&gt;/buttons/button1<br />
&lt;MasterTopic&gt;/buttons/button2<br />
:::

The MQTT response has the following structure:

```json
{
    "true/false"
}
```

## Lux Sensor

::: blue HTTP Endpoint [GET]
/api/luxsensor
:::
::: blue MQTT Topic [PUBLISH]
&lt;MasterTopic&gt;/getLuxsensor
:::
::: blue MQTT Topic [SUBSCRIBE]
&lt;MasterTopic&gt;/luxsensor
:::

The response has the following structure:

```json
{
  "lux": 107.0217
}
```

## Sensor

::: blue HTTP Endpoint [GET]
/api/sensor
:::
::: blue MQTT Topic [SUBSCRIBE]
&lt;MasterTopic&gt;/sensor
:::

The response has the following structure:

```json
{
  "humidity": 40,
  "temperature": 21.5
}
```

## State

Its possible to get a disconnection (and connection) message via the MQTT LWT (Last Will and Testament) Feature.

::: blue MQTT Topic [SUBSCRIBE]
&lt;MasterTopic&gt;/state
:::

```
disconnected (or connected)
```

## Matrix Infomation

::: blue HTTP Endpoint [GET]
/api/matrixinfo
:::
::: blue MQTT Topic [PUBLISH]
&lt;MasterTopic&gt;/getMatrixinfo
:::
::: blue MQTT Topic [SUBSCRIBE]
&lt;MasterTopic&gt;/matrixinfo
:::
The response has the following structure:

```json
{
  "pixelitVersion": "0.3.4",
  "note": "",
  "hostname": "PixelIt",
  "freeSketchSpace": 2695168,
  "wifiRSSI": -58,
  "wifiQuality": 84,
  "wifiSSID": "WiFi",
  "ipAddress": "192.168.0.137",
  "currentMatrixBrightness": 20,
  "freeHeap": 29976,
  "sketchSize": 447424,
  "chipID": 13668362,
  "cpuFreqMHz": 80,
  "sleepMode": false
}
```

## Matrix Config

::: blue HTTP Endpoint [GET]
/api/config
:::
::: blue MQTT Topic [PUBLISH]
&lt;MasterTopic&gt;/getConfig
:::
::: blue MQTT Topic [SUBSCRIBE]
&lt;MasterTopic&gt;/config
:::
The response has the following structure:

```json
{
  "matrixBrightnessAutomatic": true,
  "mbaDimMin": 20,
  "mbaDimMax": 255,
  "mbaLuxMin": 0,
  "mbaLuxMax": 250,
  "matrixBrightness": 127,
  "matrixType": 2,
  "note": "",
  "hostname": "PixelIt",
  "matrixTempCorrection": "default",
  "ntpServer": "de.pool.ntp.org",
  "clockTimeZone": 1,
  "clockColor": "#FFFFFF",
  "clockSwitchAktiv": true,
  "clockSwitchSec": 7,
  "clockWithSeconds": false,
  "scrollTextDefaultDelay": 100,
  "bootScreenAktiv": false,
  "mqttAktiv": false,
  "mqttUser": "",
  "mqttPassword": "",
  "mqttServer": "0.0.0.0",
  "mqttMasterTopic": "pixelit/",
  "mqttPort": 1883
}
```

::: blue HTTP Endpoint [POST]
/api/config
:::
::: blue MQTT Topic [PUBLISH]
&lt;MasterTopic&gt;/setConfig
:::

To set the configuration, the upper structure must be followed.

## WiFi Reset

::: blue HTTP Endpoint [GET]
/api/wifireset
:::
::: blue MQTT Topic [PUBLISH]
&lt;MasterTopic&gt;/wifiReset
:::

Erases the WiFi configuration and restarts the device. Device will be in AP mode after restart.

## Factory Reset

::: blue HTTP Endpoint [GET]
/api/factoryreset
:::
::: blue MQTT Topic [PUBLISH]
&lt;MasterTopic&gt;/factoryReset
:::

Erases the WiFi configuration and all other settings and restarts the device. Device will be in AP mode after restart.
