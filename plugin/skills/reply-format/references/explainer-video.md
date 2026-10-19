# Explainer video

After a big change, offer a video. When the user says yes, hand off to the `interactive-explanation` skill. It makes the video and cleans up after it.

## When

| Make a video | Skip the video |
|---|---|
| A new feature or a new flow | A small fix or a config change |
| Several files changed | Answers and research |
| The user says "video" or "explain it" | Nothing to show on screen |

## Steps

1. If the user did not ask for a video, end the reply with "Your move: say 'video' and I make a short explainer video of this change." Do not record it silently and do not skip it silently. Done when the offer is in the reply, or the user asked for the video.
2. When the user asks, run the `interactive-explanation` skill in `task` mode for the change. Done when it prints the path of the `architecture.mp4` video.
3. Embed the video on the first line of the reply, next to the sketch, as an
   image link with its absolute path: `![<title>](<absolute path to architecture.mp4>)`.
   Done when the link is in the reply.

Reason: a short narrated video explains a big change faster than text. A chat panel that plays video, such as a dashboard panel, turns an image link to an .mp4 into a player.
