# SlideSmith — Quick Guide & Technical Notes

## What this demo does
SlideSmith converts a block of text (markdown or plain prose) into a downloadable PowerPoint (.pptx) that tries to re-use the styling from a user-supplied template. All processing is performed in the browser: the template is read as a ZIP archive, images and theme files are copied, and new slide XML files are generated and packaged back into a .pptx for download.

## How to use
- Paste your content into the **Source content** box. Markdown headings (#, ##) will be interpreted as slide boundaries.
- Optionally add a one-line directive in the guidance input (for example: "make this an investor pitch").
- Select an LLM provider and supply your API key if you want the app to request a slide plan from a model. Leave provider empty to use the local heuristic.
- Upload a PowerPoint template (.pptx or .potx). The page extracts media from `ppt/media/` and copies layout/master files to preserve appearance.
- Click **Create Deck** to compile and download the generated presentation.

## Implementation highlights
- The app uses **JSZip** to read and write the PowerPoint package and **xml2js** is available if you want to extend XML parsing (this demo uses string templates for slide XML).
- Slide planning happens two ways:
  - **Local heuristic** — splits by markdown headings and generates bullets from paragraphs.
  - **LLM plan** — if an LLM is selected, the page sends a concise prompt to the provider and expects JSON (slides array) in return. The JSON is parsed into slide objects.
- The composer copies essential parts of the template (masters, layouts, theme, media) and writes new `ppt/slides/slideN.xml`, `ppt/presentation.xml` and the presentation relationships.
- No new images are created by any AI: only media files embedded in the uploaded template are reused.

## Privacy & limits
- Keys are used in-browser (not logged or stored). For production, route LLM calls via a server to avoid exposing keys to client-side JS.
- Keep template file sizes reasonable (recommended < 10 MB).
- The demo caps the number of generated slides to 20.

## Extending the demo
- Add more robust XML parsing to detect exact layout placeholders.
- Implement speaker notes generation by asking the LLM for per-slide notes.
- Add a preview UI that renders thumbnails of generated slides before download.
- Improve error handling and provide retry logic for LLM calls.

## Troubleshooting
- **No JSON from model**: the app expects clean JSON. Wrap model instructions with a strict `Respond with JSON only` instruction.
- **Template errors**: ensure the uploaded file is a valid .pptx/.potx and not password protected.
