# Neurocine

Landing page for an academic research project on film, world cinema, spectatorship and neurocinematics.

A single static page. No build step, no dependencies, no framework. Open `index.html` in a browser and it runs.

---

## Files

```
index.html                        the whole page: markup, styles, script
robots.txt                        crawler directives
sitemap.xml                       single-page sitemap
assets/neurocine-scanpath.mp4     sample eye-tracking result (H.264, 6.9 MB, 17s, silent)
assets/poster.jpg                 poster frame, also the social sharing image (44 KB)
assets/favicon.svg
```

Total payload on first view is about 90 KB: the page, the poster and the fonts. The video is
6.9 MB but downloads nothing until someone scrolls to it.

---

## Publish with GitHub Pages

1. Create a new repository and upload everything in this folder, keeping `assets/` intact.
2. Go to **Settings → Pages**.
3. Under **Source**, choose **Deploy from a branch**, pick `main` and the `/ (root)` folder, then save.
4. The site appears at `https://USERNAME.github.io/REPOSITORY/` within a minute or two.

The video is 10 MB, comfortably inside GitHub's 100 MB per-file limit, so plain Git works — no Git LFS required.

---

## Before it goes public

**Your published URL.** `USERNAME` appears six times — in `index.html` (canonical link, Open Graph
and Twitter tags, JSON-LD), in `robots.txt` and in `sitemap.xml`. One find-and-replace fixes all of
them. Until then, search engines index the wrong address and link previews render blank.

```
USERNAME.github.io/neurocine  →  your-actual-address
```

**Contact details.** Search `index.html` for `[Researcher Name]`, `[Institution]` and `[Email]`.
Replace all three, and remove `class="placeholder"` from each so they stop rendering in muted
italic. Update `href="mailto:email@institution.edu"` to the real address — this is now the only
route for filmmakers to reach you, since the trailer form is gone.

**Film credit spelling.** The page credits *Kudapola Unnanse 1848 — Kapila Perrera — IERI*. The
watermark inside the video reads "kadahapola unnanse 1848." Confirm which is correct.

**After deploying**, submit the sitemap in Google Search Console to get indexed faster.

---

## Replacing the sample video

Drop a new file into `assets/` and update the two paths in the `<video>` element.

Encode to **H.264 in an MP4 container**. Browsers reject MPEG-4 Part 2, DivX and most camera-native codecs, and will show a black rectangle instead of an error:

```bash
ffmpeg -i source.mov -c:v libx264 -profile:v high -pix_fmt yuv420p \
       -crf 25 -preset veryslow -tune film -movflags +faststart -an output.mp4
```

`-movflags +faststart` matters: it moves the index to the front of the file so playback can begin
before the whole thing arrives. Without it the clip stalls on slow connections.

Also regenerate the poster, since it is preloaded and shown before playback begins:

```bash
ffmpeg -ss 4 -i output.mp4 -frames:v 1 -vf "scale=1280:-2" -q:v 6 assets/poster.jpg
```

The video is styled with `object-fit: contain`, not `cover`. This is deliberate: cropping to fill the frame would cut off the eye-tracking readout in the top-left corner and the legend along the bottom edge. Keep it if your replacement also has overlaid data.

---

## How the video behaves

It does not autoplay on load and downloads nothing until it is scrolled into view — `preload="none"`
plus an IntersectionObserver. Once it starts it loops indefinitely and scrolling away does not stop
it. A play/pause control sits in the corner of the frame, always visible on touch devices.

Mobile browsers only permit muted autoplay. The clip has no audio track, so this is not a
limitation. If autoplay is refused anyway, the poster stays up and the play button appears.

---

## Licensing

Choose separate terms for the code and the footage. The sample clip is third-party film material used by permission for research illustration, so a blanket open-source licence on the repository would misrepresent it. Add a `LICENSE` file covering the page itself and leave the video's rights statement in the on-page credit.
