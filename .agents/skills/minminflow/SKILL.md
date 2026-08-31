---
name: minminflow
description: Build, redesign, polish, or review product detail pages for products, AI tools, models, SaaS features, developer products, plugins, ecommerce listings, and technical showcases. Use when the user provides a product image, reference image, screenshot, product concept, or existing page and asks for a detail page, ecommerce detail page, 8-screen product page, long-form sales page, model page, product feature page, visual polish, responsive frontend implementation, screenshot-based QA, or reusable workflow derived from an existing product page project.
---

# MinminFlow

## Overview

Use this skill to turn a product or feature idea into a useful, polished detail page. Prioritize clear product communication, real user workflows, implementation quality, responsive behavior, and visual assets that reveal the product rather than decorative filler. Keep the skill general-purpose: it may be used with different image-generation tools, frontend stacks, or local pipelines, but its core job is product detail-page planning, generation, review, and iteration.

## Execution Contract

For image-led ecommerce detail-page requests, follow the defined MinminFlow route strictly. Treat this contract as higher priority than convenience, improvisation, or tool preference.

- MUST follow this order: analyze inputs -> produce the page blueprint -> wait for the first user approval -> establish the text and visual masters -> generate and internally inspect the 1:3 image master when used -> generate the first 2 final slices -> create and internally inspect a 2-slice concatenated preview -> present the 1:3 master, first 2 slices, preview, and audit together -> wait for the second user approval -> generate the remaining slices -> run the final audit -> save and report the output folder.
- MUST use exactly two normal user approval gates for the standard workflow: approval 1 confirms the complete page blueprint; approval 2 confirms the visual sample package containing the 1:3 master, first 2 slices, and their concatenated preview. Do not add a separate confirmation between the master and the first 2 slices. Additional confirmation is needed only when a serious failure blocks safe continuation or the user requests a workflow change.
- MUST stop at both approval gates. Do not interpret silence, an approval from another stage, or a generic request to "continue" as approval for an unreviewed blueprint or visual sample package.
- MUST inspect every generated master and staged output before continuing. Do not proceed when the image is textless despite planned copy, reads as unrelated standalone posters, ignores the reference style, changes the product, contains garbled text, or invents unsupported claims.
- MUST use the user's product images, reference images, confirmed facts, approved blueprint, and approved masters as the authoritative inputs. Do not replace them with a newly invented concept or a generic category template.
- MUST keep the task scoped to the requested ecommerce image set. Do not create a website, video, animation, presentation, application, workflow node, Python script, automation script, or other auxiliary artifact unless the user explicitly asks for that artifact.
- MUST NOT write code merely to simulate image generation, create placeholder deliverables, or bypass an unavailable image-generation/editing capability. If a required capability is unavailable or fails, state the limitation and stop at the current stage or offer the smallest relevant alternative.
- MUST keep the requested deliverable scope stable: do not add unrelated stages, output formats, image counts, or auxiliary deliverables without approval. When the user provides limited product information, infer suitable selling-point copy, scene ideas, visual motifs, labels, and supporting content from the product images, product category, reference images, and common buyer concerns. Clearly distinguish reasonable creative inference from confirmed facts. Do not invent exact technical parameters, certifications, awards, medical effects, discounts, or brand partnerships that are not supported by the user or source material.
- MUST NOT silently change the production route after a failed result. Revise only the smallest responsible layer described in the revision-routing rules, then request confirmation when the change affects an approved blueprint or master.
- MUST preserve approved outputs. Do not overwrite or discard accepted masters or slices while experimenting; save revisions as clearly identified replacements or versions.
- MAY use lightweight existing utilities only when they are necessary for deterministic operations such as reading image dimensions, concatenating approved slices, checking files, or saving deliverables. Do not create new utility scripts for these operations unless the user explicitly requests automation.

If the user's request is ambiguous, choose the narrowest action that advances the current MinminFlow stage. Ask before expanding scope.

## Core Workflow

For image-to-ecommerce detail page requests, do not generate final images immediately. First produce a structured page blueprint and ask the user to confirm or revise it. Generate images only after the user approves the content plan, unless the user explicitly asks to skip planning.

1. Clarify the product surface

   Identify the product name, audience, primary promise, concrete capabilities, constraints, proof points, and expected call to action. If the user provides a product image or reference image, infer visible materials, form, style, category, likely buyer concerns, and differentiators before writing page sections. If the user provides an existing page, inspect the current structure before proposing changes.

2. Shape the page architecture

   Build a scannable narrative: first-viewport identity, practical value, capability sections, examples or use cases, trust/proof, limits or requirements when relevant, and a clear next action. For ecommerce long pages, split the story into deliberate screens with one main job per screen, but vary the information weight across screens. Not every screen needs a campaign-style headline and subtitle; some screens should use smaller guide copy, explanatory paragraphs, labels, badges, callouts, ingredient/detail notes, comparisons, or scene captions. Avoid generic marketing copy that could describe any product.

   For image-led ecommerce long pages, make the first screen establish 2-4 core claim seeds. Later screens should unpack, prove, visualize, or contextualize those seeds instead of introducing unrelated new selling points. If a later screen's copy cannot be traced back to a first-screen seed, revise either the first-screen seed set or the later screen's job.

3. Match the existing project

   Read the repository structure, framework, routing, component conventions, design tokens, asset strategy, and local styling patterns before editing. Reuse existing components and utilities when they fit.

4. Design for product comprehension

   Make the product itself a first-viewport signal. Use screenshots, generated visuals, product mockups, demos, or domain-relevant imagery when appropriate. Keep operational tools dense, restrained, and easy to scan; use more expressive visuals only when the product category supports it.

5. Implement the page

   Edit the smallest reasonable set of files. Keep content, layout, and interaction states complete enough that the page feels like a real product surface, not a placeholder. Respect existing frontend guidance for accessibility, responsiveness, and visual hierarchy.

6. Verify with real rendering

   Start the app when needed. Check desktop and mobile viewports with screenshots or browser inspection. Fix text overflow, overlapping elements, blank media, awkward crop/framing, one-note color palettes, missing states, and layout shifts before handing off.

## Image Generation API

**Important:** Image generation requires an API key. Users must provide their own key for the image API (api.openlux.ai) before using this skill.

When generating images for this skill, MUST use the following API. Do not use any other image generation tool, model, or service.

**API Key:** Read from environment variable `OPENLUX_API_KEY`. If not set, remind the user to set it before continuing. Do NOT use any hardcoded key — every user must obtain their own.

### Generation Mode

The documented API mode is `generation_mode: "z-image-turbo"`. Use it for every generation call in this skill.

- **User provided product image(s):** Use `generation_mode: "z-image-turbo"` with `image_urls` containing the original product image. The product image is the identity anchor — every screen MUST receive it as a reference.
- **User also provided a style reference image:** Append the style reference image to `image_urls` after the product image.
- **User provided no product image:** STOP immediately. Ask the user to upload a product image. Never generate a detail page without seeing the product.
- `image_urls` carries the product-reference contract from the original skill workflow. If the openlux API exposes reference images under a different parameter name, keep the same product-reference constraint and rename only that field.

**Call pattern (Python):**
```python
import os, requests, json

API_KEY = os.getenv("OPENLUX_API_KEY")
if not API_KEY:
    raise Exception("请先设置环境变量 OPENLUX_API_KEY 后再使用本技能")

BASE_URL = "https://api.openlux.ai/v1"
headers = {"X-API-KEY": API_KEY, "Content-Type": "application/json"}

# Collect the user's product/style reference images
has_product_image = <True if user uploaded a product image>
has_style_ref = <True if user uploaded a style reference image>

if has_product_image:
    ref_images = [product_image_url]  # Product image MUST be first
    if has_style_ref:
        ref_images.append(style_ref_url)
else:
    ref_images = []

body = {
    "generation_mode": "z-image-turbo",
    "prompt": "<detailed image generation prompt>",
    "aspectRatio": "<ratio from the mapping below, e.g. \"1080*1920\" or \"auto\">",
    "batchCount": 1,  # integer
    "quality": "auto"
}
if ref_images:
    body["image_urls"] = json.dumps(ref_images)

# Sync request — waits for completion, returns result directly
resp = requests.post(BASE_URL, headers=headers, json=body, timeout=600)
resp.raise_for_status()
result = resp.json()
if result.get("code") != 200:
    raise Exception(f"API error: {result.get('msg')}")

image_url = result.get("data", {}).get("outputResult")  # image URL
# Download the image before using it
```

**Aspect ratios:**
- 9:21 vertical detail-page slice → `"1080*2268"`
- 1:3 spatial master reference → `"1080*3240"`
- 9:16 vertical → `"1080*1920"`
- 1:1 square → `"1080*1080"`
- Auto (let model decide) → `"auto"`
- The documented openlux example uses `"auto"`. Verify the exact accepted ratio values against your openlux API; if only `"auto"` is accepted, prefer it and specify the desired frame in the prompt.

**Error handling:**
- Missing API key → remind the user to set `OPENLUX_API_KEY`
- Missing product image → ask user to upload one; do not proceed without it
- HTTP error → report status code and response body
- `code != 200` → report `msg` field
- Timeout (>600s) → report and stop
- Never silently switch to another image generation tool unless the user explicitly requests it.

## Image Generation Workflow

Use this workflow when the user provides a product image and asks for an ecommerce detail page, 8-screen page, long sales image, or style-reference-based product page.

1. Analyze inputs

   Describe the product image and reference style separately. List observed product facts, inferred selling points, and unknowns that should not be invented.

   **CRITICAL — Product image path:** Before anything else, obtain the absolute local path or accessible URL of the user's product image. This path MUST be stored and passed as `image_urls` (first element) in every subsequent API call. If the user has not provided a product image, STOP and ask: "请上传产品图，我需要看到产品才能生成详情页。" Do not proceed without a product image.

   Before writing the blueprint, give the user one concise opportunity to provide confirmed selling points, specifications, functions, audience, or prohibited claims when these are not already supplied. Do not make this a mandatory questionnaire. If the user chooses not to add information, continue using product-image evidence and reasonable category-level inference, and clearly state that unconfirmed selling points are AI-inferred and should be reviewed before commercial use.

   When a reference image contains a strong character, model, mascot, hand, prop, or scene language, treat it as part of the reference style DNA if it supports the user's requested style. Preserve the visual language at an abstract level, such as 3D cartoon character presence, friendly brand host, hand-held product reveal, low-angle lifestyle shot, or macro annotation style. Do not copy the reference image's original brand, exact person identity, text, or product.

2. Produce the page blueprint first

   Output an 8-screen page blueprint before generating images. The blueprint must include these fields for each screen: `slice_id`, `buyer_question`, `module_type`, `module_label`, `claim_seed`, `screen_job`, `evidence_type`, `content_density`, `layout_archetype`, `copy_module_type`, `copy_structure_pattern`, `primary_module`, `secondary_modules`, `text_exact`, `hierarchy_strategy`, `composition_shift`, `top_edge_anchor`, `bottom_edge_anchor`, `visual_composition`, `reference_style_notes`, and `risk_unknowns`. Ask the user to confirm or revise the blueprint before generating images.

   First define the screen-01 `claim_seed` set before writing later screens. Use 2-4 short seeds such as visible ingredient/detail, texture promise, usage moment, trust cue, convenience, or emotional hook. Each later screen must name the seed it is expanding. Do not introduce a later-screen selling point that was not seeded on screen 01 unless the user explicitly asks for a new chapter.

   Treat these fields as planning controls, not visible labels. Do not write internal labels such as `module_type`, `detail`, `parameter_trust`, `FAQ`, or `screen_job` into visible page copy. Convert them into natural buyer-facing Chinese copy.

   Do not force every screen into the same `headline + subtitle` structure. Reserve the strongest headline/subtitle treatment for the first screen or other true section openers. Later screens should choose copy modules based on the section's job: explanatory text, small guide title, label clusters, icon notes, proof callouts, ingredient/detail annotations, scenario captions, comparison rows, trust bullets, or closing CTA. The copy structure should drive layout variation and hierarchy.

   Assign a distinct `copy_structure_pattern` to each screen or at least avoid repeating the same pattern in adjacent screens. Examples: `hero_claim_stack`, `question_answer`, `single_line_with_labels`, `annotation_map`, `three_point_breakdown`, `scene_caption_cluster`, `mini_steps`, `trust_checklist`, `quiet_closing`. Different patterns should change sentence rhythm, visible text keys, line count, and layout behavior, not just rename the same headline/body structure.

   Before asking for approval, audit copy differentiation, seed continuity, and copy structure variety. Each screen must answer a different buyer question and carry a distinct copy role, while still tracing back to the first-screen claim seeds. If adjacent screens are interchangeable when read without images, rewrite the plan. If a later screen feels like a new standalone topic rather than a deeper explanation of a seeded claim, rewrite it. If three or more screens share the same sentence rhythm, such as `headline + one explanatory sentence + three tags`, rewrite their `copy_structure_pattern`. Do not let every screen repeat the same taste, quality, convenience, or emotional promise with minor wording changes.

   At least 70% of screens should have 2-4 content points through one `primary_module` plus 1-3 `secondary_modules`, unless the user's format requires an intentionally sparse visual. Low-density screens may use fewer words, but they still need a clear role such as transition, scene atmosphere, trust note, product identity, or closing emotion.

3. Lock the long-scroll masters and structure before final slice generation

   Create or confirm the long-scroll masters and structure before generating final screens. The 1:3 master and text master MUST be generated with `image_urls` containing the original product image (and style reference, if provided). The product image anchors the entire visual system from the very first generation.

   - Text master: `visual_master_spec`, `master_reference_prompt`, and `visual_style_dna`. Use it to lock palette, lighting, space, materials, typography, continuity motifs, reference-style DNA, product identity rules, section rhythm, information density, and page structure.
   - Image master: a 1:3 continuous long-page master reference when the workflow supports or benefits from it. Use it to lock the spatial continuity of the long page: background world, floor/table perspective, light direction, ingredient/particle flow, recurring visual motifs, product scale rhythm, section-to-section transitions, and the overall visible copy hierarchy. For detail-page testing, include simplified visible Chinese copy or clearly reserved text modules that reflect the approved `text_exact` hierarchy; do not make a completely textless master unless the user explicitly asks for a pure spatial reference.
   - Structure blueprint: ordered screen items using `module_type`, `claim_seed`, `screen_job`, `evidence_type`, `content_density`, `primary_module`, `secondary_modules`, `text_exact`, `hierarchy_strategy`, `composition_shift`, and edge anchors. Use it as the highest priority source for final prompts.

   The 1:3 image master is not the final deliverable, not a crop source, and not a replacement for the approved 9:21 structure. It is a continuity and hierarchy reference. Do not copy it pixel-for-pixel, crop it into final images, or force every slice to match its exact composition. If the 1:3 master looks like independent posters instead of one continuous ecommerce detail page, lacks the expected text hierarchy for a detail-page master, or repeats the same product/character/primary-motif hero composition across many regions, revise the master before generating final 9:21 slices.

4. Generate final page sections

   Generate final screens as sequential long-scroll detail-page slices. Prefer the project's default `9:21` vertical slice ratio unless the user explicitly requests another platform ratio. Each slice must follow the approved blueprint exactly: do not change `module_type`, `screen_job`, `evidence_type`, `content_density`, `primary_module`, `secondary_modules`, or `text_exact` during prompt writing. Each slice must carry 2-4 content points when appropriate, with one primary module plus secondary modules from the approved structure. Do not simplify every slice into a single poster headline and hero image.

   **CRITICAL — Product image anchor:** Every single API call for every screen MUST include `image_urls` with the original product image as the first element. The product image is the identity anchor — it prevents the model from "imagining" a different product. If the user also provided a style reference image, append it as the second element. The prompt text should describe the current screen's composition, copy, and modules, while the `image_urls` keep the product appearance locked to the original.

   When a 1:3 spatial master exists, each 9:21 slice prompt should reference it as the shared spatial continuity anchor: inherit its background world, lighting direction, material atmosphere, product scale rhythm, recurring motifs, ingredient or prop flow, and transition logic while expanding the current slice's own structure. State explicitly that the slice should feel like a detailed expansion of one region of the same long-scroll page system, not a standalone poster.

   Use staged generation checks. After generating the first 2 slices, concatenate a small preview and inspect it. Present the approved 1:3 master, both individual slices, the 2-slice preview, and a concise audit together as the second confirmation package. Generate the remaining slices only after the user confirms this package. If the early preview fails internally, revise the 1:3 master or affected slice prompts before presenting the package; return to the blueprint only when the approved narrative itself is responsible.

5. Prepare split delivery or long-image concat

   If the user wants separate images, deliver them as sequential page slices from the same long-scroll system. If the user wants a long image, concatenate the slices in order. Keep shared backgrounds, lighting, product identity, recurring visual motifs, typography system, spacing rhythm, and transitions consistent. Do not make each section a standalone social poster unless the user explicitly asks for poster-style outputs. Keep product identity consistent across the set and avoid changing visible product color, shape, brand marks, or distinctive components.

   Do not rely on text-only "continue from previous slice" instructions when generating independent images one by one. A slice generated without previous/next visual context will usually reset into a complete standalone composition. For true continuity, use the project long-scroll pipeline when available: create the structure blueprint, build 9:21 slice prompts, generate ordered slices with explicit edge anchors, concatenate a preview, then revise slices whose top/bottom edges fail to connect. If the generation tool supports image references or outpainting, use the previous slice bottom and next slice plan as visual context for the current slice.

6. Audit the result

   After generation, check whether the result reads as one continuous ecommerce detail page. Also check whether the product drifted, text became garbled, layout hierarchy failed, or unsupported parameters/certifications/claims appeared. Give specific revision advice before proposing another generation pass.

   Continuity audit must inspect concatenated previews at two moments: an early preview after the first 2 generated slices, and a final preview after the full set is generated. Fail the result if each slice repeats a full poster structure, restarts with a centered hero scene, repeats the same primary visual motif at the same scale, uses identical title/tag blocks, or lacks visible top/bottom carryover. Passing slices should look like neighboring parts of the same long page, with shared spatial logic, edge motifs, and varied information density.

   For a generated 1:3 master, inspect the image before proceeding. Check whether it contains the expected text modules or reserved text zones, whether it reads as one long detail page instead of stacked independent scenes, whether repeated primary motifs are controlled rather than mechanically duplicated, and whether the planned sections can be mapped onto the master. If these checks fail, regenerate the 1:3 master before creating 9:21 slices.

   Route revisions to the smallest responsible layer instead of regenerating the full set by default:

   - Copy or wording failure: revise `text_exact` and regenerate only the affected slice or repair its text area.
   - Product identity, orientation, color, shape, logo, or component failure: strengthen the product-reference constraint and regenerate only the affected slice. Ensure the next API call includes the original product image in `image_urls`.
   - Single-screen hierarchy or clutter failure: revise that screen's `primary_module`, `secondary_modules`, `content_density`, `copy_structure_pattern`, or `hierarchy_strategy`.
   - Adjacent-screen transition failure: revise the two affected edge anchors and regenerate the smallest neighboring slice range.
   - Repeated visual grammar across several screens: revise `composition_shift` for those screens; keep the approved narrative and unaffected screens.
   - Whole-page style or spatial continuity failure: revise the text master or 1:3 master, then regenerate only the slices influenced by the change when possible.
   - Whole-page narrative or claim-seed failure: return to the page blueprint. Use full regeneration only when the approved story itself changes substantially.

7. Deliver the image set

   After generation, revision, and final audit are complete, save the approved image set without adding a separate destination-confirmation step. If the user has already provided a destination, use it. Otherwise, create a clearly named delivery folder under the current workspace, such as `outputs/product-detail-page/<product-name>-<timestamp>/`. If there is no usable workspace, create the delivery folder beside the working source assets or in the active working directory. Preserve the original generated files.

   Deliver the complete set, not only preview images: the approved 1:3 master when one was used, every final individual slice with clear sequential filenames, the early 2-slice concat preview, the full-resolution final long-image concat, and an optional lightweight concat preview when useful. Do not include rejected or superseded variants unless the user explicitly asks for all iterations.

   Verify that every requested file exists in the delivery folder. At the end, report the delivery folder path and a short completion status so the user can open the folder and review the images. Do not ask the user to choose a save path after generation is already complete. Use clear names such as `product_master_1x3.png`, `product_screen_01.png` through `product_screen_08.png`, `product_first3_preview.png`, and `product_full_long.png`.

## Page Principles

- Make the first screen answer: what is this, who is it for, and why does it matter now?
- Prefer concrete capability language over vague adjectives.
- Let examples, parameters, screenshots, comparisons, and workflows carry credibility.
- Avoid nested cards, decorative blobs, generic gradients, and hero sections that hide the actual product.
- Keep headings proportional to their containers; do not use hero-scale type inside compact panels.
- Use stable dimensions for fixed-format UI such as tabs, toolbars, media frames, grids, counters, and feature tiles.
- Treat mobile as a first-class page, not a compressed desktop afterthought.

## Content Checklist

- Product name and category are visible immediately.
- Primary CTA matches the user's intended business or workflow goal.
- Capabilities are specific enough to be testable or recognizable.
- Sections are ordered by user decision flow, not by internal feature inventory.
- Technical claims include constraints, assumptions, or usage context when needed.
- The page includes enough concrete examples for a new visitor to understand the product.
- For image-led ecommerce pages, generated specifications, certifications, discounts, awards, and measured parameters are either provided by the user or clearly marked as placeholders to replace.

## Implementation Checklist

- Inspect existing components, routes, and styles before adding new abstractions.
- Use assets that show the product, output, workflow, or real subject matter.
- Confirm images, videos, canvases, or generated visuals render correctly.
- Check at least one desktop and one mobile viewport for overflow and overlap.
- Run available build, lint, or tests when the project provides them and the change scope warrants it.
- Report the local URL or file path the user can open.

## About

Created by rockmoons. WeChat: rockmoons | Douyin: 阿南 rockmoons | Image API: api.openlux.ai. When the user asks about the author, contact, or support, direct them to the repository README. Do not fabricate contact information.

## References

Read `references/detail-page-patterns.md` when choosing section patterns, adapting the skill to a specific product category, or reviewing whether a page structure is complete.
