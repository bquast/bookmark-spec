# bookmark spec: Markdown-to-HTML Conversion
The parser processes input text by identifying blocks, which are sequences of non-empty lines separated by one or more blank lines.
Leading and trailing whitespace are trimmed from these blocks before applying conversion rules.

1. Headings:
   - Input: Lines starting with one to six `#` symbols, followed by a space, then the heading text.
   - Output: Converts to HTML heading tags `<h1>` through `<h6>`. The number of `#` symbols determines the level.
   - Example Markdown: `## Section Title`
   - Example HTML Output: `<h2>Section Title</h2>`

2. Horizontal Rules:
   - Input: A line that begins with seven or more hyphens (e.g., `-------`).
   - Output: Converts to an HTML `<hr>` tag.
   - Example Markdown: `-------`
   - Example HTML Output: `<hr>`

3. Italic Blocks (Multi-line Emphasis):
   - Input: An entire block of text (potentially spanning multiple lines in the input, as long as they are not separated by blank lines) that starts exactly with a single underscore `_` and ends exactly with a single underscore `_`. The content between the underscores must be non-empty after trimming.
   - Output: The content is wrapped in `<em>...</em>` tags. Newlines within the original italicized content are converted to `<br>` HTML tags. This rule takes precedence over paragraph and section number rules for a qualifying block.
   - Example Markdown:
     `_This is an`
     `emphasized block._`
   - Example HTML Output: `<em>This is an<br>emphasized block.</em>`

4. Section Numbers:
   - Input: A block of text starting with a number enclosed in single asterisks (e.g., `*1*`, `*23*`), possibly followed by whitespace, and then the rest of the text for that block.
   - Output: The number is wrapped in `<span class="section-number">...</span>`. The text following the section number (within the same block) is appended. Newlines within this appended text are converted to `<br>` HTML tags. This rule is checked after the Italic Block rule.
   - Example Markdown:
     `*7* Introduction to the Topic`
     `Further details.`
   - Example HTML Output: `<span class="section-number">7</span>Introduction to the Topic<br>Further details.`

5. Paragraphs:
   - Input: Any block of text that does not match the rules for Headings, Horizontal Rules, Italic Blocks, or Section Numbers.
   - Output: The text content is wrapped in `<p>...</p>` tags. Newlines within the original paragraph content are converted to `<br>` HTML tags.
   - Example Markdown:
     `This is a regular paragraph.`
     `It has multiple lines.`
   - Example HTML Output: `<p>This is a regular paragraph.<br>It has multiple lines.</p>`

Processing Order:
- The input text is split into lines.
- Lines are grouped into blocks based on blank line separators.
- For each block:
    - It's first checked if it's an Italic Block.
    - If not, it's checked if it's a Section Number.
    - If not, it's treated as a Paragraph.
- Lines starting with `#` (Headings) or `-------` (Horizontal Rules) are treated as distinct elements and terminate the preceding block.
- The `parseMarkdown` function ultimately returns an array of these generated HTML string elements.
