# Figma Integration Standards

## Overview

All UI implementations must be based on Figma designs. The official Figma MCP (Model Context Protocol) must be used to fetch design context before implementing any visual component.

## Figma Design File

- **File Key:** Stored in `FIGMA_FILE_KEY` environment variable
- **URL Format:** `https://www.figma.com/design/<FIGMA_FILE_KEY>/<file-name>`
- **Main Page Node:** `933-20670`

## Required Workflow

When implementing any UI component or page from a Figma design:

### 1. Get Design Context

Always use `mcp__figma__get_design_context` first to fetch design details:

```
Parameters:
- nodeId: The node ID from the Figma URL (e.g., "5226:12590" from ?node-id=5226-12590)
- fileKey: Use value from `FIGMA_FILE_KEY` environment variable
- clientLanguages: "typescript"
- clientFrameworks: "react,nextjs"
```

### 2. Get Screenshot for Visual Reference

After getting design context, use `mcp__figma__get_screenshot` for visual reference:

```
Parameters:
- nodeId: Same as above
- fileKey: Use value from `FIGMA_FILE_KEY` environment variable
```

### 3. Implement Both Variants

- Always implement **mobile AND desktop** versions from the Figma design
- Use responsive breakpoints as defined in the CSS standards

## Extracting Node ID from URL

Given a Figma URL like:

```
https://www.figma.com/design/<fileKey>/<fileName>?node-id=5226-12590
```

Extract:

- **fileKey:** The alphanumeric string after `/design/` and before the file name
- **nodeId:** `5226:12590` (convert hyphen to colon from the `node-id` query param)

## Available MCP Tools

| Tool                             | Purpose                                                        |
| -------------------------------- | -------------------------------------------------------------- |
| `mcp__figma__get_design_context` | Get detailed design information (styles, layout, colors, text) |
| `mcp__figma__get_screenshot`     | Get visual screenshot of a node                                |
| `mcp__figma__get_metadata`       | Get node structure overview (use sparingly)                    |
| `mcp__figma__get_variable_defs`  | Get design system variable definitions                         |

## Best Practices

1. **Always verify with Figma before implementing** - Don't assume; check the actual design
2. **Use design tokens** - Extract colors, spacing, and typography from Figma and map to existing Tailwind classes
3. **Match exact spacing** - Use Figma's spacing values, converting to appropriate Tailwind utilities
4. **Verify responsive behavior** - Check both mobile and desktop frames in Figma
5. **Extract assets properly** - Use the asset URLs provided in design context for images and icons
