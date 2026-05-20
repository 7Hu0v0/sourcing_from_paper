---
name: cvpr-industry-scout
description: >
  Analyze CVPR/top-conference papers to identify the paper direction, whether the paper has
  industry involvement from major AI/tech organizations, list the exact industry institutions,
  group industry-affiliated authors by company, mark school+company authors as interns, and
  find likely LinkedIn profiles with evidence. Use for CVPR paper author/institution scouting,
  industry paper screening, LinkedIn sourcing, and Excel/CSV annotation of paper lists.
---

# CVPR Industry Scout

Use this skill when the user provides one or more CVPR/top-conference papers, paper titles,
PDF/arXiv/OpenReview/CVF links, or an Excel/CSV paper list and asks to classify industry
involvement, paper direction, authors by institution, intern status, or LinkedIn profiles.

## Core Output

For each paper, return or write these fields:

| Field | Required content |
|---|---|
| Paper direction | One short Chinese phrase, e.g. `视频生成`, `3D视觉`, `多模态大模型`, `自动驾驶感知` |
| Industry paper? | `是` / `否` / `存疑` |
| Industry institutions | Exact company/lab names from the paper affiliation text. List multiple when present |
| Authors by institution | Group only industry-affiliated authors under each company/lab |
| Intern marker | If an author has both school and company affiliations, keep only the company group and append `（实习生）` |
| LinkedIn | For every listed industry author, provide the likely LinkedIn URL or `未找到/存疑`, with brief evidence |
| Evidence | Paper source URL plus affiliation evidence source; LinkedIn/source URL for each match |

Preferred compact format:

```markdown
### {Paper Title}
- 论文方向：{direction}
- 工业界：{是/否/存疑}
- 工业机构：{institution1; institution2}
- 作者归类：
  - {Institution}: {Author A}, {Author B（实习生）}
- LinkedIn：
  - {Author A} ({Institution}): {url} — {evidence}
  - {Author B} ({Institution}, 实习生): {url or 未找到} — {evidence}
- 依据：{paper_url}; {affiliation_source}
```

## Non-Negotiable Rules

- Do not infer industry affiliation from memory, a person's reputation, or a search snippet alone.
- Use the paper's own affiliation text as the primary source for institutions.
- If the paper page/PDF has no reliable affiliations, mark institution and industry status as `存疑`.
- Keep similar companies separate unless the paper explicitly writes the same parent/lab:
  Alibaba is not Ant Group; ByteDance is not Kuaishou; Huawei is not Horizon Robotics.
- If an author has both university and company affiliations, classify them under the company only and mark `（实习生）`.
- If multiple industry institutions appear, list all and group authors separately.
- For LinkedIn, return only profiles with enough evidence to match name + institution/domain. Otherwise write `未找到` or `存疑`.
- Do not fabricate LinkedIn URLs.

## Workflow

1. **Collect paper source**
   - Prefer official CVF/OpenReview/conference page, arXiv HTML/PDF, publisher PDF, or project page with author affiliations.
   - For PDFs, inspect the first page and author footnotes; for arXiv, use HTML when available and PDF when needed.

2. **Determine paper direction**
   - Use the title, abstract, and venue keywords.
   - Write a short Chinese direction, not a long abstract summary.

3. **Extract author affiliations**
   - Build a per-author mapping: `Author -> all affiliation strings`.
   - Preserve exact institution names as written in the paper before normalizing.
   - If affiliation symbols are ambiguous, inspect footnotes, author order, supplementary page, and project page.

4. **Identify industry institutions**
   - Match exact affiliations against known industry organizations and aliases.
   - Read `references/industry-institutions.md` when the paper mentions major AI/tech institutions or ambiguous labs.
   - Treat these as industry/non-academic for this task: major companies, company research labs, Shanghai AI Laboratory, BAAI, Peng Cheng Laboratory, A*STAR, and other applied research institutes if the user wants industry-style sourcing.

5. **Group authors**
   - Include only authors who have an industry affiliation.
   - If author has `University + Company`, group under the company and append `（实习生）`.
   - If author has multiple companies, list under each relevant company and mark the ambiguity in evidence.

6. **Find LinkedIn**
   - Search with strong queries:
     - `"{Author Name}" "{Company}" LinkedIn`
     - `"{Author Name}" "{Paper Title}" LinkedIn`
     - `site:linkedin.com/in "{Author Name}" "{Company}"`
     - For Chinese names, also try known pinyin variants only if supported by the paper or profile evidence.
   - A good match needs at least two signals: same name, current/past company, university, paper topic, coauthor network, personal page, Google Scholar, or GitHub.
   - If multiple plausible profiles exist, write `存疑` and list the top evidence instead of choosing blindly.

7. **Excel/CSV mode**
   - Auto-detect title and author columns by header names such as `Title`, `Paper`, `Authors`.
   - Add columns if missing:
     `论文方向`, `是否工业界`, `工业机构`, `工业作者按机构`, `LinkedIn`, `依据`.
   - Process in batches and save after each batch.
   - Keep original title/author columns unchanged unless the user asks for formatting.

## Classification Details

Use `工业界=是` when at least one author has an industry/non-academic affiliation from the paper.

Use `工业界=否` when all authors are universities or clearly academic institutes and affiliations are fully visible.

Use `工业界=存疑` when:
- the paper cannot be found;
- author affiliations are hidden, incomplete, or conflict across sources;
- an institution is ambiguous and cannot be verified quickly;
- LinkedIn/profile evidence contradicts the paper affiliation.

## Common Failure Modes

- University-led paper with one or two company authors in the middle of the author list.
- Company labs written as department names, e.g. `Seed`, `Qwen`, `Tongyi`, `DAMO Academy`, `FAIR`, `Reality Labs`, `MSRA`.
- Authors with dual affiliations; these are often interns and must be marked.
- People changing jobs; always use the affiliation on the paper for institution grouping, not their current LinkedIn employer.
- Search result summaries saying only a university while the full PDF includes company footnotes.

## Evidence Standard

Every positive industry classification must have:
- a paper/affiliation source URL or file reference;
- exact industry institution text from the paper;
- author-to-institution mapping;
- LinkedIn evidence for each listed profile, or a clear `未找到/存疑`.

When writing citations in prose, prefer short evidence notes over long quotes.
