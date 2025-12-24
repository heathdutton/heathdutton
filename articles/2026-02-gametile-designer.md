<picture>
  <img src="https://ghlogo.heathdutton.workers.dev/heathdutton/gametile-designer?ratio=3:2" alt="Gametile Designer" width="100%">
</picture>

# Game Tiles for Picky Humans

This has bugged me for years. Hexes, squares, triangles - board game tiles tessellate beautifully in theory, but in practice they slide around the moment someone bumps the table. There's no interlocking geometry. You're just trusting friction and hope. Some of us (not naming names) get really upset when the pieces do not line up EXACTLY right.

Some games solve this with a frame (looking at you, Catan), but that's a hack that constrains your layout options. Puzzle pieces figured out interlocking geometry decades ago, but why not use a little bit of that concept for a board game?

I'd been mentally noodling on the math for a while. Twist-lock joints seemed like the answer: small rotational features on each edge that let tiles drop in from above and resist lateral movement. Even a tiny bit of twist would increase friction significantly allowing pieces to stay aligned more easily. The geometry isn't complicated, but visualizing how the interactions across different base shapes is the kind of thing that's hard to do in your head.

This felt like the right excuse to finally try do (gasp) v~i~b~e coding.

## The Tool

**[Gametile Designer](https://heathdutton.github.io/gametile-designer/)** is a single-page app for designing soft interlocking board game tiles. Pick a base shape (hex, square, or triangle), dial in your twist parameters, and watch the geometry update in real time. When you're happy with it, export an SVG and feed it to a laser cutter.

The controls let you adjust things like:

- **Twist angle and radius** - how aggressively the tiles interlock
- **Edge and vertex rounding** - soften the geometry for aesthetic or safety reasons
- **Side smoothing** - S-curves between joints for organic-looking edges, if that's your thing
- **Tile thickness** - because material depth affects how the joints behave physically

There's a 3D preview so you can see how tiles sit together, physics gauges that (very roughly) estimate interlock strength and material durability, and a demo mode that cycles through parameters if you just want to watch the shapes morph.

A contained, single-page tool with no backend, no dependencies, and a clear goal. This is the perfect use-case for a little bit of LLM-based code. I described the geometry, the mathmatic assumptions and the interactions I wanted, and let Claude write the whole thing. It's a single HTML file, so the context is dense and there's less risk of error due to context rot.

For something this self-contained, it worked surprisingly well. The entire app fits in context. There's no sprawling dependency tree or multiservice architecture to trip over. Just geometry, SVG, and a handful of sliders. I was even able to drop in a screenshot here and there to say "can you see what you did wrong here?". I suspect this is how a lot of developers scratch creative itches now - you've got 20 minutes while a deploy runs, and the barrier between "I wonder if..." and a working prototype has basically collapsed.

## Try It

**[gametile-designer](https://heathdutton.github.io/gametile-designer/)**

If you have access to a laser cutter, export an SVG and see how the tiles feel in your hands. If you don't, the demo mode is still oddly satisfying to watch.

---

[View on GitHub](https://github.com/heathdutton/gametile-designer) ・ [Back to Profile](https://github.com/heathdutton)
