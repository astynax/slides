# slides

This repo contains the slides for my recent talks.

### remark-based slideshows: howto

Some of slideshows were built using

- [remark](https://github.com/gnab/remark) (JS-powered slideshow tool)
- [ltext](http://ltext.github.io/) (file templating utility)
- [hobbes](https://github.com/jhickner/hobbes) (file activity monitor)

To create such slideshow one should

- run `bin/new my_next_slides`
- edit `my_next_slides.title` (*)
- edit `my_next_slides.md` (*)
- call either
  - `bin/build my_next_slides` to build `.html` once
  - `bin/watch my_next_slides` to live rebuild on each change of `.title`/`.md` file

(*) - you should preserve the first empty line of `.md`/`.title` files (ltext needs that).
