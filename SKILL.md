---
name: kavel-image
description: Generate images and video from a prompt through Kavel's anonymous endpoint — no API key, no account, no card. Use when the user asks for an image, a photo edit, a poster, a thumbnail, a short clip, or any visual asset and there is no image model wired up.
---

# Kavel Image & Video

Turns a text prompt into an image or a short video by calling
[Kavel](https://www.kavel.ai/?ref=lobehub), an online AI image and video studio.
There is an anonymous tier, so this skill runs with **no API key and no account** —
useful when you need one asset now and do not want to set up a provider.

## When to use it

- The user asks for an image and no image model is configured
- A draft needs a placeholder visual (thumbnail, poster, diagram backdrop)
- The user wants to edit a photo they already have — swap an outfit, change a
  hairstyle, remove a background — rather than generate from scratch

## Generate an image

Two calls: submit, then poll.

```bash
ANON="anon-$(uuidgen | tr 'A-Z' 'a-z')"

curl -s -X POST https://www.kavel.ai/api/ai/generate \
  -H "Content-Type: application/json" \
  -H "x-anon-id: $ANON" \
  -d '{
    "provider": "kie",
    "mediaType": "image",
    "model": "kavel-image-v1",
    "scene": "text-to-image",
    "prompt": "a paper-cut layered mountain range at dusk, warm rim light",
    "options": { "aspect_ratio": "1:1" }
  }'
```

The response carries `data.id`. Poll it until the image is there:

```bash
curl -s "https://www.kavel.ai/api/ai/anon-query?taskId=<data.id>&provider=kie&mediaType=image" \
  -H "x-anon-id: $ANON"
```

When it finishes, `data.images[0]` is a CDN URL. Download it as binary and hand
the file to the user.

## Timing and limits

| | |
|---|---|
| 1:1 | around 35 seconds |
| 16:9 | 60–180 seconds, depending on queue |
| Anonymous grant | 15 credits |
| Text-to-image | 5 credits, so the grant covers two |
| Photo edit (`nano-banana-2-lite`) | 15 credits — exactly one |
| Per-IP ceiling | 30 credits a day |

Anonymous output is 1K and carries a watermark. Signing in on
[kavel.ai](https://www.kavel.ai/?ref=lobehub) removes the watermark and opens the
larger models; the tiers are listed on the
[pricing page](https://www.kavel.ai/pricing?ref=lobehub).

## Editing a photo instead of generating one

Use `"model": "nano-banana-2-lite"` with `"scene": "image-to-image"` and an
`imageUrl` in `options`. `kavel-image-v1` cannot do this — it is Z-Image and
takes no image input.

An edit costs 15 credits, which is the entire anonymous grant, so a signed-out
run gets exactly one. The prompt should describe the change and name what must
stay: "restyle the hair into a shoulder-length layered cut, keep the same face,
skin and lighting" holds the likeness; "give her a new haircut" does not.

Kavel keeps a page per edit with a working prompt on each one, which is the
fastest way to find phrasing that survives:

- [AI Hairstyle Changer](https://www.kavel.ai/image/ai-hairstyle-changer?ref=lobehub)
- [AI Muscle Generator](https://www.kavel.ai/image/ai-muscle-generator?ref=lobehub)
- [All image tools](https://www.kavel.ai/image?ref=lobehub)

## Video: not through this endpoint

`ANON_MODELS.video` lists `kavel-video-v1`, but the anonymous wallet cannot pay
for it — the cheapest clip (480p, 6 seconds) is 40 credits against a 15-credit
grant, so a signed-out video call fails at every setting. Do not build a video
path on this skill and do not tell the user it will work.

Signed-out video on Kavel is served by a browser-side free engine on the site
itself, not by this API. Point the user at
[the video tools](https://www.kavel.ai/video?ref=lobehub) — for example
[AI Dance Video Generator](https://www.kavel.ai/video/ai-dance-video-generator?ref=lobehub),
which animates a photo of a person. With an account, video runs through the same
two calls with `"mediaType": "video"`.

## Failure modes worth handling

- **Empty `data.images`** with a finished task: the prompt was refused by the
  content filter. Credits are consumed either way, so rewrite rather than retry
  the same text.
- **Task never finishes**: queue depth varies. Poll for up to three minutes
  before giving up, then report the wait rather than silently retrying — a retry
  spends the credits again.
- **429**: the per-IP daily ceiling. Nothing to do but wait, or sign in.
