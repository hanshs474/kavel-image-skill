# kavel-image

An agent skill for generating images from a prompt with no API key and no account,
by calling [Kavel](https://www.kavel.ai/?utm_source=skill&utm_medium=github)'s anonymous endpoint.

Most image skills need a provider key before they do anything. This one does not:
Kavel has an anonymous tier, so a fresh install can produce a picture on the first
call. Read from the running service on 2026-10-02: a client id is granted 5 credits,
one text-to-image run costs 5, and one IP gets two images a day. Output is 1K and
watermarked. Photo edits and video need a Kavel API key, and the skill says so
instead of attempting them.

## Install

Drop `SKILL.md` into your agent's skills directory. No configuration, no
environment variables.

## What it covers

- text-to-image through the anonymous submit-and-poll flow
- how to check the current free grant before spending it
- photo edits with an API key, and what to offer without one
- why video does not run anonymously, and where to send the user instead
- the failure modes that actually happen (quota wall, refusals, filter rejections,
  queue waits) and what to do about each

## Links

- [Kavel](https://www.kavel.ai/?utm_source=skill&utm_medium=github): the studio itself
- [Image tools](https://www.kavel.ai/image?utm_source=skill&utm_medium=github): one page per edit, each with a working prompt
- [API keys](https://www.kavel.ai/settings/apikeys?utm_source=skill&utm_medium=github)
- [Pricing](https://www.kavel.ai/pricing?utm_source=skill&utm_medium=github): what signing in adds

## License

MIT
