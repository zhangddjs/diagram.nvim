# TODO

- [x] according to the setting opts={xxx}, there is an option `scale`, but it doesn't work, can you find the issue and fix it.
    opts = {
      renderer_options = {
        mermaid = {
          scale = 2, -- nil | 1 (default) | 2  | 3 | ...
        },
      },
    },
- [x] can you add a trigger, that when I press `K`, the image will be rendered in a float window (like normal `K` hover does), if cursor is in the markdown mermaid codeblock
- [x] the `K` hover is slow, can you add a spin upon loading
