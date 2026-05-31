# Python Reading Materials

A curated collection of Python learning notes written as an [Obsidian](https://obsidian.md/) vault. Each note is a self-contained topic with deep explanations, runnable code examples, and links to related notes so you can read straight through or jump around by concept.

The material is organized in stages — from language foundations through intermediate topics and beyond — with numbered notes inside each stage folder. Notes are connected with **Prev / Next** navigation at the top and **See also** / **What's next** sections at the bottom.

---

## Getting Obsidian

Obsidian is a free note-taking app that reads plain Markdown files from a folder on your computer. This project is designed to be opened as an Obsidian **vault** (that folder).

### Download and install

1. Go to [https://obsidian.md/download](https://obsidian.md/download).
2. Download the installer for your operating system (macOS, Windows, or Linux).
3. Run the installer and open Obsidian when it finishes.

Obsidian is free for personal use. You do not need an account to open a local vault.

---

## Opening this project in Obsidian

1. **Get the files** — clone or download this repository to your machine:

   ```bash
   git clone <repository-url>
   cd python-reading-materials
   ```

   Or download the ZIP from GitHub and extract it.

2. **Open Obsidian** and choose **Open folder as vault** (on first launch, this is the main option on the welcome screen).

3. **Select the project root folder** — the directory that contains this `README.md` and the stage folders (e.g. `01 - Foundations`). That entire folder becomes your vault.

4. **Trust the vault** if Obsidian asks — the `.obsidian` folder in the project stores local editor settings (theme, plugins, graph view). It is safe to open.

5. **Start reading** — open any note from the file explorer on the left, or begin with `01 - Foundations/01 - Getting Started.md`.

### Tips while reading

- **Wiki-links** — notes link to each other with double brackets, e.g. `[[03 - Strings]]`. In Obsidian, click a link to open that note.
- **Graph view** — use the graph icon in the left sidebar to see how topics connect.
- **Backlinks** — open a note and check the **Backlinks** panel to see which other notes reference it.
- **Code blocks** — examples are plain Python; copy them into a REPL or `.py` file to run them. Many examples include `# output` comments showing expected results.

---

## Structure of each note

Every learning note in this vault follows the same **document anatomy**. Section names may vary slightly by topic, but the overall pattern is consistent. Below is the exact structure and what each part is for.

### 1. YAML frontmatter

At the very top of the file, between `---` delimiters:

```yaml
---
tags:
  - foundations
  - python
  - loops
stage: 1
difficulty: Beginner
---
```

| Field | Purpose |
|-------|---------|
| `tags` | Topic labels for search and filtering in Obsidian (e.g. `python`, `strings`, `control-flow`) |
| `stage` | Learning stage number (`1` = Foundations, `2` = Intermediate, etc.) |
| `difficulty` | Rough level: `Beginner`, `Intermediate`, or similar |

Some notes may include extra frontmatter fields (e.g. `status: draft`) as the collection grows.

---

### 2. Title (H1)

A single `#` heading with the topic name:

```markdown
# The `for` Loop
```

This is the note title as it appears in Obsidian's file list and graph.

---

### 3. Navigation line

Immediately under the title — links to the previous and next note in the reading order:

```markdown
**Prev:** [[01 - Getting Started]] | **Next:** [[02 - Variables and Data Types]]
```

- The first note in a stage uses `**Prev:** —`.
- Links use Obsidian **wiki-link** syntax: `[[File name without path]]`.
- Follow **Next** links to read the series in order, or use **Prev** to go back.

---

### 4. One-sentence summary (blockquote)

A short `>` blockquote that states the core idea in one line:

```markdown
> A `for` loop walks through an **iterable** — a list, string, `range`, file, or any object that yields items one at a time.
```

Read this first for a quick sense of what the note covers before diving into the sections below.

---

### 5. Main content sections

The body is written in Markdown with `##` and `###` headings. Topics differ, but notes typically include some combination of the following **section types**:

#### Why this matters

Explains the real-world motivation — why you need this concept and what goes wrong without it.

#### Core concept / What is …?

Defines the topic in plain language. Often includes:

- **Tables** for operators, syntax, or comparisons
- **Prose** with bold terms for vocabulary
- **Step-by-step** explanations of how Python evaluates or executes something

#### Syntax

Shows the general form before examples:

```python
for variable in iterable:
    # body — runs once per item
```

#### Code examples

Runnable Python in fenced code blocks. Conventions used throughout:

- **Output comments** on the same line or following line: `# 7`, `# True`, `# apple`
- **Anti-patterns** prefixed with `# Wrong` or shown as comments that would error
- **Before/after** pairs when comparing two approaches

```python
for fruit in ["apple", "banana"]:
    print(fruit)
# apple
# banana
```

#### Deep dives / Nested topics

Subsections (`###`) for harder areas — e.g. nested loops, short-circuit evaluation, truthiness tables — with extra diagrams, walkthroughs, or comparison tables.

#### Common mistakes / Gotchas

Bullet list of frequent errors, misconceptions, and Python-specific surprises (e.g. `is` vs `==`, mutable `+=`, loop variable reuse).

#### Best practices

Short, actionable guidelines for writing idiomatic Python related to the topic.

#### Summary

Optional recap bullet list at the end of the main content (used in some earlier notes).

---

### 7. See also

Cross-references to **related notes** elsewhere in the vault — not necessarily the next note in sequence, but topics worth reading in parallel or revisiting:

```markdown
## See also

- [[09 - While loop]] — condition-driven repetition when the count is unknown
- [[07 - Comparisons and Logical Operators]] — membership with `in` when filtering in a loop
```

Each line is a wiki-link plus a brief note on **why** that link is relevant.

---

### 8. What's next

A closing bridge to the **next recommended note** in the learning path:

```markdown
## → What's next

You can walk through sequences and nested grids. Loops also need fine-grained control — stopping early, skipping items, and leaving placeholders — with [[11 - break, continue, pass]].
```

Unlike **See also** (sideways links), **What's next** points forward and explains how the current topic leads into the next one.

---

## Example: annotated outline

Below is how the pieces fit together in a typical note (structure only — not full content):

```markdown
---
tags: [...]
stage: 1
difficulty: Beginner
---

# Topic Title

**Prev:** [[Previous Note]] | **Next:** [[Next Note]]

> One-sentence summary of the topic.

---

## Why this matters
...

## Core concept
...

## Code examples
```python
# example with output comments
```

## Common mistakes / Gotchas
...

## Best practices
...

## See also
- [[Related Note]] — why it is related

## → What's next
Transition sentence with link to [[Next Note]].
