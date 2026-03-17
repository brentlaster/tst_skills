# Branding Slide Templates

This file contains PptxGenJS code patterns for Brent's standardized branding slides.
These assets are in the `assets/` directory of this skill.

## Asset Inventory

| File | Purpose | Used In |
|------|---------|---------|
| `image1.tiff` | Brent's profile photo | Title slide |
| `image4.png` | Company logo / TM mark | Title slide |
| `image5.png` | Secondary branding element | Title slide |
| `image3.png` | Branding element | Ending slide |
| `image11.jpeg` | Book cover (Professional Git) | Ending slide |
| `image12.jpeg` | Book cover (Learning GitHub Actions) | Ending slide |
| `image113.jpeg` | Book cover (Learning GitHub Copilot) | Ending slide |
| `image112.png` | Social/branding icon | Ending slide |
| `image114.png` | Website/branding graphic | Ending slide |
| `image17.png` | Supplementary branding | Ending slide |

**Important:** When building slides, resolve the path to these assets relative to the
skill's `assets/` directory. The skill user's deck generation script should reference
these files by their absolute path.

## Version Slide (Slide 1)

```javascript
// Slide 1: Version tracking
let versionSlide = pres.addSlide();
versionSlide.background = { color: "FFFFFF" };
versionSlide.addText(`Version 1.0 | ${new Date().toLocaleDateString('en-US')}`, {
  x: 0.3, y: 0.2, w: 4, h: 0.4,
  fontSize: 12, fontFace: "Poppins", color: "999999"
});
```

## Title Slide (Slide 2)

```javascript
// Slide 2: Title slide with branding
let titleSlide = pres.addSlide();
titleSlide.background = { color: "011936" };

// Talk title
titleSlide.addText("[TALK TITLE]", {
  x: 0.8, y: 1.0, w: 8.4, h: 1.5,
  fontSize: 36, fontFace: "Lato", color: "FFFFFF",
  bold: true, align: "center", valign: "middle"
});

// Subtitle (optional — use if talk has a subtitle)
titleSlide.addText("[SUBTITLE IF ANY]", {
  x: 0.8, y: 2.4, w: 8.4, h: 0.6,
  fontSize: 20, fontFace: "Poppins", color: "FFFFFF",
  align: "center", valign: "middle"
});

// Presented by line
titleSlide.addText("Presented by Brent Laster &", {
  x: 5.5, y: 3.2, w: 4, h: 0.4,
  fontSize: 14, fontFace: "Poppins", color: "FFFFFF",
  align: "right"
});

// Company name
titleSlide.addText("Tech Skills Transformations LLC", {
  x: 5.5, y: 3.5, w: 4, h: 0.4,
  fontSize: 14, fontFace: "Poppins", color: "FFFFFF",
  align: "right"
});

// Copyright
const year = new Date().getFullYear();
titleSlide.addText(`© ${year} Brent C. Laster & Tech Skills Transformations LLC`, {
  x: 0.5, y: 4.7, w: 9, h: 0.3,
  fontSize: 10, fontFace: "Poppins", color: "FFFFFF",
  align: "center"
});

titleSlide.addText("All rights reserved", {
  x: 0.5, y: 4.95, w: 9, h: 0.3,
  fontSize: 10, fontFace: "Poppins", color: "FFFFFF",
  align: "center"
});

// Profile image (bottom-left area) — adjust path to assets directory
titleSlide.addImage({
  path: "[ASSETS_PATH]/image1.tiff",
  x: 0.5, y: 3.0, w: 1.5, h: 1.5,
  rounding: true
});

// Branding logos — adjust positions as needed
titleSlide.addImage({
  path: "[ASSETS_PATH]/image4.png",
  x: 8.5, y: 0.3, w: 1.0, h: 0.6
});
```

## Ending Slide (Last Slide)

```javascript
// Last slide: Thank you + contact
let endSlide = pres.addSlide();
endSlide.background = { color: "FFFFFF" };

// Title
endSlide.addText("That's all - thanks!", {
  x: 0.5, y: 0.3, w: 9, h: 1.0,
  fontSize: 36, fontFace: "Lato", color: "002060",
  bold: true, align: "center"
});

// Contact line
endSlide.addText("Contact: training@getskillsnow.com", {
  x: 0.5, y: 1.2, w: 9, h: 0.4,
  fontSize: 16, fontFace: "Poppins", color: "0070C0",
  align: "center"
});

// Book covers (arrange horizontally)
const bookY = 2.2, bookH = 2.5, bookW = 1.7;
endSlide.addImage({
  path: "[ASSETS_PATH]/image11.jpeg",  // Professional Git
  x: 1.5, y: bookY, w: bookW, h: bookH
});
endSlide.addImage({
  path: "[ASSETS_PATH]/image113.jpeg",  // Learning GitHub Copilot
  x: 4.15, y: bookY, w: bookW, h: bookH
});
endSlide.addImage({
  path: "[ASSETS_PATH]/image12.jpeg",  // Learning GitHub Actions
  x: 6.8, y: bookY, w: bookW, h: bookH
});

// Website URLs
endSlide.addText([
  { text: "techskillstransformations.com", options: { breakLine: true } },
  { text: "getskillsnow.com" }
], {
  x: 0.5, y: 4.8, w: 9, h: 0.6,
  fontSize: 14, fontFace: "Poppins", color: "0070C0",
  align: "center"
});
```

## Notes for Implementation

- Replace `[ASSETS_PATH]` with the actual absolute path to this skill's `assets/` directory
  when generating slides. The script should resolve this at runtime.
- Replace `[TALK TITLE]` and `[SUBTITLE IF ANY]` with the actual talk information.
- The profile image (`image1.tiff`) may need conversion to PNG for better compatibility
  with PptxGenJS. Convert with: `sharp(tiffBuffer).png().toFile('profile.png')`
- Book cover images are JPEG — these work directly with PptxGenJS.
- The exact positioning may need adjustment based on how many elements are included.
  The coordinates above are starting points based on the original deck layout.
