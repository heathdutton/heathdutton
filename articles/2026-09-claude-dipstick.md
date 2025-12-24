<picture>
  <img src="https://ghlogo.heathdutton.workers.dev/heathdutton/claude-dipstick?ratio=3:2" alt="claude-dipstick" width="100%">
</picture>

# Checking the Oil on Claude Code

The default status line gives you the model and the folder. I knew both when I opened the terminal.

What I wanted:

- context left
- how close I am to the five-hour cap
- whether the prompt cache is about to expire and bill me full price

All three ride in on stdin already. Claude Code hands the status line a JSON blob every render, `rate_limits` included. Same numbers a usage tracker burns a round trip to go fetch.

So [claude-dipstick](https://github.com/heathdutton/claude-dipstick) reads that blob and draws it. Nothing to call out to, nothing running in the background.

<img src="https://raw.githubusercontent.com/heathdutton/claude-dipstick/main/images/callout.png" alt="what each part of the line is" width="100%">

The README says what each mark means. This is the rest of it.

## Everything is a fork

A status line renders constantly. It has to be free, or it becomes the thing you notice.

My first version parsed the payload with nine `echo | grep | sed` pipelines, and you could feel every one.

Every `$( )` in bash is a fork, about 2ms here. Nine pipelines at ~8ms each cost more than the whole rest of the render, all of it string handling. It's one `grep` pass and parameter expansion now. No helper functions either, since `$(helper)` forks too.

Once you start counting forks you can't stop:

- Git state comes off the filesystem instead of `git`. `HEAD` is text with the branch name in it. A `.git` directory is a normal checkout, a `.git` FILE is a linked worktree. `rev-parse` and `branch` cost ~10ms each to say the same.
- Unpushed is the same trick, branch ref against the remote's copy, both files already. I skipped ahead/behind counts (those really do need git).
- The transcript scan uses `grep` over `awk`. Same work, 5ms against 57ms. On the 60MB transcript I keep around for this, 13ms against 835ms.

One subprocess survived .. `git status`, for the dirty file count, ~12ms.

## Claude Code redraws your line

It doesn't hand your string to the terminal. It parses the line into a model of its own and re-serialises. Three things fell out of that.

The default palette color is `0;36`, and that leading `0` is a reset. Write it after a background and it wipes the background out from under you, which matters because the line reuses colors as backgrounds when a bar fills. There's a conversion function now, and the segments draw in the order that keeps it from happening again. Cost me an afternoon, and the fix is just knowing.

Underlines lose everything but the boolean. I wanted links marked with a dotted underline in a dimmer tint. Dotted arrives solid. The underline color doesn't arrive at all. So you get the loud solid rule the dotted version existed to avoid. iTerm2 draws both correctly through `cat`, so it isn't the terminal's fault. The hierarchy moved somewhere that survives the round trip. Each link's glyph is tinted to 60% of its label, so the word lands first and the icon trails it.

The five-hour and weekly numbers don't exist till Claude Code's first response of the session. My first cut omitted those bars, so the context meter opened at nearly the full width of the terminal and collapsed to a third of it a second later, every session. The layout holds the room empty now. Eight columns for a reset time nobody can read yet.

## Model families are shapes

Sides descend with capability: circle, hexagon, square, triangle, dot.

<img src="https://raw.githubusercontent.com/heathdutton/claude-dipstick/main/images/models.gif" alt="each model named as it renders" width="100%">

Letters would have been easier and I'd have hated them. `OM` announces to the room that I'm running Opus at max effort. A shape announces nothing.

Haiku's dot went wrong twice. First pick rendered as nothing at all. It lived in the private-use range down in the BMP, macOS system fonts claim that range too, and it lost the fallback race. Second pick rendered fine and looked like a runt, because I'd eyeballed it. The four large marks are all 1233 font units wide. The dot was 617.

I read the rest out of the font file after that. Same story with the dirty-files marker, where a bare `±` and the Octicons diff glyph both read as arithmetic, till I found one in a box.

## What it won't do

The cost figure is list price, computed client-side. On a Max plan that's what the session WOULD have cost, which is why it's hidden there by default.

The weekly bar is worse. Local tally, this Mac only, and only sessions whose status line actually rendered. Good for noticing a bad week (useless for arguing with an invoice).

It also wants a Nerd Font. There are fallbacks for the caps and shapes and they're fine, but I won't pretend they're the same thing.

## Try it

Paste this at Claude Code and let it do the wiring:

```
Install the status line from https://github.com/heathdutton/claude-dipstick: put statusline.sh at
~/.claude/statusline.sh, then point statusLine (refreshInterval 60) and subagentStatusLine at it
in ~/.claude/settings.json.
```

Config and fallbacks are in the [README](https://github.com/heathdutton/claude-dipstick#readme). Clone it rather than curling the one file and you get `tools/preview.sh` too, which plays the animations against a throwaway `HOME` and a made-up account. You can watch the meters fill without burning five hours to see it happen.

---

[View on GitHub](https://github.com/heathdutton/claude-dipstick) ・ [Back to Profile](https://github.com/heathdutton)
