# Import Google Doc Comments into Markdown

Use Playwright MCP to extract comments from a Google Doc and insert them into an existing markdown file at their appropriate locations.

## Prerequisites

- User must provide an existing markdown file containing the document content (without comments)
- User must provide the Google Doc URL that contains the comments

## Instructions

1. Open the Google Doc page at the provided URL using Playwright
2. Open the comments sidebar by clicking "Show all comments" (or via View menu)
3. Collect all comments using the scroll-and-capture loop:
   - Take a snapshot of the comments panel
   - Record all visible comments (quoted text, author, date, content, replies)
   - Scroll the comments panel to the bottom using `browser_evaluate`:
     ```javascript
     () => {
       const list = document.querySelector('[role="complementary"] [role="list"]');
       if (list) {
         const prevScroll = list.scrollTop;
         list.scrollTop = list.scrollHeight;
         return { scrolled: true, prevScroll, newScroll: list.scrollTop };
       }
       return { scrolled: false };
     }
     ```
   - Take another snapshot after scrolling
   - If new comments are visible that weren't captured before, record them
   - **Repeat** until scrolling reveals no new comments (scroll position doesn't change or no new content appears)
4. Copy the original markdown file to a `_with_comments` variant (e.g., `GDD.md` → `GDD_with_comments.md`)
5. For each comment collected, immediately:
   - Use `Grep` tool to search the `_with_comments` file for the quoted/highlighted text
   - Find the exact line number where the referenced text appears
   - Use `Edit` tool to insert the comment callout block immediately after the matching text
   - The file is saved automatically after each edit

**Critical**:
- Use `browser_snapshot` to read comment content from the sidebar
- The comments sidebar shows which text each comment is attached to (usually quoted)
- Use `Grep` to find matching text in the markdown file ad-hoc - do NOT read the whole file upfront
- If exact text match fails, grep for key phrases or section headings near the comment

## Comment Callout Format

Insert comments immediately after the referenced text in the markdown:

```markdown
[Referenced text or paragraph from the document]

> [!COMMENT] by [Author] ([Date])
> [Comment content]
>
> **Reply by [Author] ([Date]):**
> [Reply content]
```

## Output

- By default, creates a new file with `_with_comments` suffix (e.g., `GDD.md` → `GDD_with_comments.md`)
- Original markdown file remains unchanged

## Arguments

- Google Doc URL (required) - source of comments
- Markdown file path (required) - existing file to augment with comments

$ARGUMENTS
