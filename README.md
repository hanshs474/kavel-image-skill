# kavel-image

An agent skill for generating images and short video from a prompt with no API
key and no account, by calling [Kavel](https://www.kavel.ai/?ref=lobehub)'s
anonymous endpoint.

Most image skills need a provider key before they do anything. This one does not:
there is an anonymous tier (15 credits, 5 per text-to-image, 30 per IP per day),
so a fresh install can produce a picture on the first call. Output is 1K and
watermarked; signing in removes both.

## Install

Drop `SKILL.md` into your agent's skills directory. No configuration, no
environment variables.

## What it covers

- text-to-image and image-to-image (photo edits that keep the face)
- why the anonymous wallet cannot pay for video, and what to do instead
- the failure modes that actually happen — filter refusals, queue waits, the
  per-IP ceiling — and what to do about each

## Links

- [Kavel](https://www.kavel.ai/?ref=lobehub) — the studio itself
- [Image tools](https://www.kavel.ai/image?ref=lobehub) — one page per edit, each with a working prompt
- [Video tools](https://www.kavel.ai/video?ref=lobehub)
- [Pricing](https://www.kavel.ai/pricing?ref=lobehub) — what signing in adds

## License

MIT
