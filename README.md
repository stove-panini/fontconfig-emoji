# fontconfig-emoji

## Deprecation Notice
At the time of writing these configuration files, proper prioritization of the "Emoji" font family hadn't made its way into most distros. This has since changed! Fontconfig currently works its magic right out of the box nowadays.

As of this writing, I'm running Fedora 33 with none of these customizations applied and everything works as intended.

DejaVu _may_ still remain a problem for reasons described below, so you might have to blacklist that.

### tl;dr
1. Drop `70-no-dejavu.conf` in `~/.config/fontconfig/conf.d/`
2. Enjoy your emoji!

Firefox users can set `font.name-list.emoji` to `emoji` in `about:config` to override its built-in "Twemoji Mozilla" font.

---

### What's the deal with these mismatched emoji?
In a standard fontconfig installation, the file `45-generic.conf` assigns most emoji fonts to a new generic parent family called *emoji* and to a new parent language, *und-zsye*. The file `60-generic.conf` then sets the fonts in preferential order within the family.

However, it never sets the priority of the generic emoji family in relation to other generic families like sans or serif! So if any fonts contain emoji characters and are in a prioritized family, those emoji get rendered first. This is what causes some emoji to be from DejaVu, and others (the ones not present in DejaVu) from your "real" emoji font.

### The fix
My `69-emoji.conf` sets up the priority like this:

1. The family declared by the page (whether specific like Arial or generic like sans-serif)
2. The emoji family
3. All the rest

The emoji family can't be top priority because it contains very common characters like 0-9, some arrows, ® and ™, etc. We want the declared font in these cases.

### About DejaVu
[DejaVu](https://en.wikipedia.org/wiki/DejaVu_fonts) is the default generic family for most distros. It's a fork of the older [Bitstream Vera](https://en.wikipedia.org/wiki/Bitstream_Vera), extended to cover more than just the Latin alphabet and common punctuation.

In addition, DejaVu covers some emoji. On a web page (or in a system UI element) where only a generic family (like sans) is called, DejaVu will render all of its emoji before handing things off to the proper emoji family.

The most painless fix is to blacklist DejaVu with `70-no-dejavu.conf`. The reason we're blacklisting rather than uninstalling is because the DejaVu package is usually marked as a dependency for several important packages on your system.

If you'd like to keep around something similar to DejaVu that doesn't include emoji, consider using the parent font from which it's forked: Bitstream Vera.

### Emoji in common families
You may find that some other non-emoji fonts have emoji characters in them as well. This is due to the historical [Wingdings](https://en.wikipedia.org/wiki/Wingdings) and [Webdings](https://en.wikipedia.org/wiki/Webdings) fonts from the 1990s being absorbed into Unicode and picked up by common fonts long before there were emoji versions of the characters.

For example, ☺ (Unicode U+263A, White Smiling Face) is supported by the Liberation family (Microsoft equivalents) as well as the URW family (PostScript equivalents). So, if ☺ is on a page declaring Arial, Times, Helvetica, etc, the non-emoji version will be rendered.

*Side note: This could be why another very similar smiley exists in the emoji set. Maybe* 🙂 *(Unicode U+1F642, Slightly Smiling Face) is intended to be used as the modern plain smiley in order to avoid the font confusion that arises with the old U+263A.*
