# Slug
This repository contains reference shader implementations for the Slug font rendering algorithm. The code itself contains many comments providing details about the inputs and the calculations performed.

For information about how the core algorithm works, see the 2017 paper in the Journal of Computer Graphics Techniques:

https://jcgt.org/published/0006/02/02/

Updated information about changes that have occurred over the years can be found in the following blog post:

https://terathon.com/blog/decade-slug.html

The code in this repository may be freely used by anyone for any purpose. The patent has been dedicated to the public domain. If you do use this code in software that gets distributed in any way, then you are required to give credit.

## Tips and Tricks

* The curve texture should have four 16-bit floating-point channels that hold control point coordinates (x1, y1, x2, y2). The first two control points for one quadratic Bézier curve are packed into one texel, and the third control point is stored in the first two channels of the next texel. When following a contour composed of connected Bézier curves, the second texel for one curve is also the first texel for the next curve because they share an endpoint.
* The band texture should have two 16-bit unsigned integer channels. A glyph can have any number of horizontal and vertical bands, and the number you choose should be optimized to minimize the maximum number of curves in any single band. When determining which curves fall into each band, use an epsilon such as 1/1024 in em-space to make the bands overlap slightly. The curves in each band must be sorted in descending order of their maximum x coordinate (for horizontal bands) or y coordinate (for vertical bands).
* Each band for a single glyph must have the same thickness, but if two or more adjacent bands end up containing the same set of curves, then you can just point all of them to the same data. It's also possible that some bands use a contiguous subset of the curves contained in another band, and in that case, you can point the smaller band to the subset of the data for the larger band. (These optimizations aren't required, but they reduce the data size and improve texture cache performance.)
* This algorithm ignores font hinting, but you can still ensure that the tops and bottoms of many glyphs are vertically aligned to the pixel grid by choosing the right font size. Grab the sCapHeight field of the 'OS/2' table in the original font, and make sure that the font size multiplied by sCapHeight is an integer. Don't forget to account for monitor DPI when determining how many pixels tall glyphs will be. [Radical Pie](https://radicalpie.com/) applies this scaling throughout its user interface if you'd like to see an example.
