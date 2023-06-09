# 2023-02-19 - deterministic DPM++ SDE across different batch sizes
DPM++ SDE and DPM++ SDE Karras samplers used to produce different images in batches compared to single image with same parameters. PR https://github.com/AUTOMATIC1111/stable-diffusion-webui/pull/7730 fixes this. But the nature of the fix also changes what generates for single images. an option is added to compatibility settings to revert to old behavior: Do not make DPM++ SDE deterministic across different batch sizes.

# 2023-01-23 - Alternating words syntax bugfix
If you used alternating words syntax bugfix with emphasis before [97ff69ef](https://github.com/AUTOMATIC1111/stable-diffusion-webui/commit/97ff69eff338c6641f4abf430bf5ac112c1775e0), the program would incorrectly replace emphasized part with just `(`. So, `[a|(b:1.1)]`, rather than becoming a sequence of

`a` -> `(b:1.1)` -> `a` -> `(b:1.1)` -> ...

becomes

`a` -> `(` -> `a` -> `(` -> ...

The bug was fixed. If you need to reproduce old seeds, put the opening parenthesis into your prompt yourself (`[a|\(]`)

# 2023-01-03 - Hires fix rework
Rather than using width/height to specify target resolution, width/height is used to specify first pass resolution, and resulting resolution is either set using "Scale by" multiplier (Hires upscale), or directly using "Resize width to" and/or "Resize height to" (Hires resize).

Here's how old and new settings correspond to each other:

| Old version                               | New version                                                                                     |
|-------------------------------------------|-------------------------------------------------------------------------------------------------|
| Size: 1024x1024                           | Size: 512x512, Hires upscale: 2.0                                                               |
| Size: 1280x1024, First pass size: 640x512 | Size: 640x512, Hires upscale: 2.0; Alternatively Size: 640x512, Hires resize: 1280x1024                                                               |
| Size: 1024x1280, First pass size: 0x0     | Size: 512x576 (auto-calcualted if you use old infotext - paste it into prompt and use ↙️ button), Hires upscale: 2.0                     |
| Size: 1024x512, First pass size: 512x512  | Size: 512x512, Hires resize: 1024x512 |

# 2023-01-01 - Karras sigma min/max
Some of discussion is here: [PR](https://github.com/AUTOMATIC1111/stable-diffusion-webui/pull/4373)

To revert to old sigmas (0.1 to 10), use setting: `Use old karras scheduler sigmas`.

# 2022-09-29 - New emphasis implementation
New implementation supports escape characters and numerical weights. A downside of the new implementation is that the old one was not perfect and sometimes ate characters: "a (((farm))), daytime", for example, would become "a farm daytime" without the comma. This behavior is not shared by the new implementation which preserves all text correctly, and this means that your saved seeds may produce different pictures.

For now, there is an option in settings to use the old implementation: `Use old emphasis implementation`.

More info about the feature: [Attention/emphasis](https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/Features#attentionemphasis)
