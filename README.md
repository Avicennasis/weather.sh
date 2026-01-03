# Weather.sh TTS (NWS API + espeak)

A small Bash & python script that generates a spoken (or speakable) local weather report for a given location, (defaults to Pittsburgh, PA_ using the National Weather Service API (`api.weather.gov`). It pulls **current conditions**, **optional forecast**, and **active alerts**, then outputs TTS-friendly text that you can pipe into a speech engine — or have it call `espeak` directly.

## Why this exists

NOAA/NWS Weather Radio MP3 links that used to work (old `srh.noaa.gov/images/rtimages/.../nwr/audio/*.mp3` paths) are no longer reliable and often 404.

Instead of scraping or chasing moving audio URLs, this project fetches live structured data from the official NWS API and produces speech output locally.

## Features

* Current conditions near location (temperature, humidity, wind)
* Active alerts for the Pittsburgh point (if any)
* Optional hourly forecast (opt-in)
* Wind direction expansion (S → South, SW → Southwest, etc.)
* Unit expansion for speech (Fahrenheit / miles per hour)
* Two modes:

  * Text output (default) for piping / logging
  * Direct speech (`SPEAK=1`) with `espeak`
* Configurable via environment variables (uppercase or lowercase)

## Requirements

### Required

* `bash`
* `python3` (uses only the Python standard library)

### Optional (for audio playback)

* `espeak` (only if `SPEAK=1`)

Install examples (Ubuntu/Debian):

```bash
sudo apt-get update
sudo apt-get install -y python3 espeak
```

## Quick start

Make the script executable:

```bash
chmod +x PIT.sh
```

### Default: current conditions + alerts (no forecast)

```bash
./PIT.sh
```

### Include forecast (hourly) + extra details

```bash
DETAILED=1 PERIODS=6 ./PIT.sh
```

### Speak out loud (no piping needed)

```bash
SPEAK=1 VERBOSE=1 DETAILED=1 PERIODS=6 ./PIT.sh
```

### Pipe into your own TTS pipeline

If you prefer controlling TTS yourself:

```bash
DETAILED=1 PERIODS=6 ./PIT.sh | espeak -v en-us -s 155
```

## Configuration

The script supports both uppercase and lowercase env var names (so both styles work).

### Location / Office / Forecast source

* `WFO` (default: `PBZ`) — NWS office ID
* `GRID_X` (default: `72`) — grid X
* `GRID_Y` (default: `62`) — grid Y
* `LAT` (default: `40.4406`) — point latitude (used for current conditions + alerts)
* `LON` (default: `-79.9959`) — point longitude
* `FORECAST_PATH` (default: `forecast/hourly`) — can be `forecast` or `forecast/hourly`

Example:

```bash
LAT=40.4406 LON=-79.9959 FORECAST_PATH="forecast" DETAILED=1 ./PIT.sh
```

### Output controls

* `DETAILED` (default: `0`)

  * `0`: skip forecast, still prints current conditions + alerts
  * `1`: include forecast (and precip/humidity if available)
* `PERIODS` (default: `6`) — number of forecast periods to read
* `FULL_ALERT_TEXT` (default: `0`)

  * `0`: alert headline only
  * `1`: include full description + instructions (can be long)
* `DEBUG` (default: `0`) — prints request debug logs to stderr
* `VERBOSE` (default: `0`)

  * When `SPEAK=1`, prints spoken text while speaking (via `tee`)
  * When piping output, can mirror speech text to stderr

### TTS controls (only if `SPEAK=1`)

* `SPEAK` (default: `0`) — call `espeak` directly
* `ESPEAK_BIN` (default: `espeak`)
* `ESPEAK_VOICE` (default: `en-us`)
* `ESPEAK_SPEED` (default: `155`)
* `ESPEAK_ARGS` (default: empty) — extra `espeak` args
* `ESPEAK_WAV` (default: empty) — if set, write a WAV file (path)

Example:

```bash
SPEAK=1 VERBOSE=1 ESPEAK_SPEED=140 ESPEAK_VOICE=en-us ./PIT.sh
```

Write a WAV:

```bash
SPEAK=1 VERBOSE=1 ESPEAK_WAV="pittsburgh-weather.wav" DETAILED=1 ./PIT.sh
```

## Notes on the NWS API / User-Agent

NWS requests a descriptive `User-Agent` identifying your application. Set:

* `NWS_UA="your-app-name (contact: email@example.com)"`

Example:

```bash
NWS_UA="pit-weather-tts (contact: me@domain.com)" ./PIT.sh
```

## Troubleshooting

### No forecast appears

Forecast is intentionally opt-in. Set:

```bash
DETAILED=1 ./PIT.sh
```

### Debug API requests

```bash
DEBUG=1 ./PIT.sh
```

### No audio plays

Only `SPEAK=1` triggers audio playback:

```bash
SPEAK=1 ./PIT.sh
```

Or pipe into your own TTS:

```bash
./PIT.sh | espeak -v en-us -s 155
```

## License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
## Credits
**Author:** Léon "Avic" Simmons ([@Avicennasis](https://github.com/Avicennasis))

