# Evidence Lab — static debate card cutter

## Files and setup

All application HTML, CSS, and JavaScript are in index.html. No build tools, backend, database, or paid hosting are required. Dependencies are downloaded on demand from third-party CDNs; this is not an offline-first distribution.

1. Put index.html at the top level of a GitHub repository.
2. In repository Settings > Pages, select Deploy from a branch, main, / (root), Save.
3. Open the published HTTPS site. Do not rely on opening the HTML with file:// for model loading or clipboard access.
4. Fetch an article or paste the original text. Verify extraction and citation.
5. Enter the claim/research question. Choose local WebLLM or Gemini BYOK, and Analyze & cut.
6. Inspect the model verdict, tag, complete excerpt, and highlighted sentences. Click sentences to toggle highlighting; change paragraph bounds to restore context.
7. Acknowledge human review only after checking the source. Export RTF/HTML, copy rich text, print/PDF, or save a project JSON.

Only index.html is needed on GitHub Pages. Never commit a Gemini API key.

## Architecture

URL -> direct browser fetch -> Mozilla Readability (when allowed by source CORS)
Fallback, only with consent: Jina Reader -> editable extracted text
Alternative: import TXT/HTML or paste original source text
Indexed paragraphs/sentences with original source offsets -> model selects paragraph bounds and sentence IDs -> strict response validation -> literal source-only renderer -> human review -> exports

The source snapshot is the text in the source editor, not a certified byte-for-byte representation of the publisher's HTML/PDF. HTML extraction and Jina can change layout or lose content. Compare with the original. No internal omission ellipses are generated. Entire consecutive paragraphs remain in the card, including unread sentences. Highlighting is sentence-level rather than aggressive word-level cutting.

Citation: full author/editor, publication date, source/publication, article title, access date, full URL, qualifications when available, pages where applicable. Missing fields are flagged, not invented. Metadata extraction checks HTML metadata, article JSON-LD, and Readability. Jina fallback extracts only its header title/date; author and publisher require manual entry. No cross-site author biography research is performed.

The event dropdown records a formatting preference; it does not implement event-specific adjudication, speech rules, or tournament compliance. The shared card format is tag + written citation + original excerpt with marked reading.

## Model options

### Local WebLLM
Default: Qwen2.5-3B-Instruct-q4f16_1-MLC, using @mlc-ai/web-llm 0.2.79. First load can download multiple gigabytes; WebGPU and adequate device memory are required. A 16,384-token context is requested. App source limit: 12,000 characters. Large model downloads and shader compilation are not instantaneous; Stop discards the result but may not immediately cancel a download. If loading fails, check browser GPU support, use a model ID supported by the pinned WebLLM release, or use Gemini. The model ID is configurable. A small local model may make substantive reasoning errors even when its JSON is valid.

### Gemini BYOK
Default model: gemini-2.5-flash; model ID is editable. Uses REST generateContent with JSON MIME type and responseSchema, then client-side validation. Source limit: 180,000 characters. Your key is held only in the password input/browser memory, never saved in projects or localStorage. Source text and argument are sent to Google only after consent and Analyze & cut. Availability, rate limits, free-tier eligibility, data handling, and billing depend on your account/provider terms. Do not assume that all keys or models are free.

This is a personal bring-your-own-key prototype, NOT a secure way to ship an application-owned secret. Browser extensions, compromised dependencies, and XSS can access a browser-held key. For a shared production service, add an authenticated backend/proxy with secret storage, quotas, abuse protection, and appropriate CORS. GitHub Pages cannot run that backend. Never embed a shared key in HTML, JavaScript, a public repository, or build-time environment variables shipped to the browser.

## Exports and privacy

- RTF: real formatted RTF with Unicode escaping, underline, yellow highlighting, and full selected excerpt. Opens in common word processors; verify imported formatting.
- HTML: standalone formatted card.
- Rich clipboard: HTML + plain text; plain-text fallback explicitly warns when highlighting is lost.
- Print/PDF: browser print dialog; output depends on print settings.
- Project JSON: complete indexed source, metadata, range, selected sentence IDs, tag, research goal, saved model assessment, and operation log. No API key. Keep it private if source text/research is sensitive. No automatic localStorage.
- Project imports reset human review and do not treat saved AI analysis as verified.
- Draft warning appears in exports until the user acknowledges review.

RTF/HTML/print contain only the continuous selected excerpt, not the entire source. Keep the original source separately and save project JSON for the full text snapshot. Project JSON is not a substitute for the publisher's original.

No PDF import, OCR, paywall bypass, account login scraping, server-side scraping, .docx writer, multi-card batch generation, or semantic fact-checking is included. Reader services may be unavailable, rate-limited, incomplete, or require authentication. Pasting source text remains the reliable manual fallback.

## Validation performed

JavaScript syntax checked with Node. Automated tests passed for paragraph separation, reconstruction of paragraphs from sentence segments, source-offset substring identity, unique sentence IDs, Unicode handling, and literal HTML-like source text. No real-browser/GPU/API end-to-end test was performed in the development environment. Test your chosen browser, GPU, Gemini account, extraction sites, and word processor before tournament use.

Manual checks:
1. Paste an article with qualifiers such as "not", "only when", and "may". Verify highlighted reading preserves meaning.
2. Request a claim the article contradicts; inspect unsupported verdict, which should have no selected reading.
3. Confirm a nonexistent sentence ID or invalid paragraph range is rejected without applying the draft.
4. Change source text and confirm export requires reindexing.
5. Export Unicode quotation text to RTF and verify Word/Docs rendering.
6. Save/reopen JSON, confirm highlights persist and human review resets.
7. Test a blocked URL with fallback off, then use paste/import or explicitly consent to Jina.
8. Check that no key appears in project JSON or repository contents.

## Research basis

NSDA evidence guide (written citation fields, original-source access, internal ellipses rule):
https://www.speechanddebate.org/wp-content/uploads/Debate-Evidence-Guide.pdf
Current rules/manuals portal (verify tournament-specific rules):
https://www.speechanddebate.org/rules-forms-manuals/
Evidence marking explanation:
https://www.congressionaldebate.org/resources/evidence-challenge
WebLLM:
https://webllm.mlc.ai/docs/user/basic_usage.html
Gemini pricing and keys:
https://ai.google.dev/gemini-api/docs/pricing
https://ai.google.dev/gemini-api/docs/api-key
Gemini generateContent structured output:
https://ai.google.dev/gemini-api/docs/generate-content/structured-output
Jina Reader:
https://jina.ai/reader/
Browser CORS:
https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS
GitHub Pages:
https://docs.github.com/articles/creating-project-pages-manually

This project is not affiliated with NSDA. Ethical evidence use remains the debater's responsibility.
