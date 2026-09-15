---
name: copyedit
description: Rewrite prose to a plain register. Removes AI writing tells, rhetorical decoration, and stiffness; front-loads the lede; names headings precisely.
disable-model-invocation: true
---

# Copyedit

**Clank** is prose that performs instead of informing. It arrives in three shapes, and an edit that knows only one leaves the other two standing. A **tell** is a mechanical marker of machine drafting, where every hit is a defect: a vocabulary word, an em dash, a rule of three, a trailing participle. Prose is **purple** when a sentence spends words on its own delivery: the knowing aside, the closer with a snap to it, the aphorism, the intensifier propping up a fact that stood fine without it. Prose is **stilted** when it holds the reader at arm's length in manual-speak: "Obey the sequence of these steps", the same subject noun opening four sentences in a row.

The target between the poles is plain: the technical thing in the subject slot, ordinary words, uneven sentence length, no ornament.

## The loop

1. Read the source. Given a writing sample from the author, read it first and match its sentence lengths, vocabulary, paragraph openings, and recurring phrases. The sample outranks every rule below, the dash ban in **Dashes** included.
2. Draft the rewrite with every rule below applied.
3. Audit the draft against four questions, answered briefly: what still reads as machine-written, what went stilted, does the first sentence of the document and of each section carry its main claim, and does any fact, name, number, date, or citation appear that the source does not have?
4. Rewrite into the final version. It is done when both greps below run clean on it: the mechanical one comes back empty, and every surviving hint hit is named with the reason it stands.

**Laundering** is the failure that survives a careless loop: a rewrite that keeps the sentence's shape and swaps its words reads as fixed and is not. Audit the diff rather than the finished text, where laundering stands out beside the original. The loop is unfinished while one sentence it touched has not been through step 3.

Check **drift** in the same pass: every surviving claim that names a path, command, count, or flag, verified against the file it describes. A rewrite is when stale claims surface, and an edit made elsewhere in the same session can falsify a sentence the loop just polished.

## Information rule

Of each sentence: **what does a reader lose if this goes?** The fact, the path, the command, the flag, the reason the thing is this way, the constraint that bites when ignored, and a unit, bound, or default all stay. A number the reader derives by reading the next few lines is not one of these. Commentary on the fact just stated and reassurance that a mistake is harmless both go.

Every claim in the source survives the rewrite, with one exception. Where stripping clank leaves a sentence saying nothing, or leaves it restating what the surrounding text or a symbol name already says, cut it instead of rewording it.

Never add a fact, name, number, date, quote, or citation absent from the source. Trading a vague claim for a specific one is allowed only when the specific comes from the source or from the author. A sentence that needs real-world detail to work gets the plain version without it, or a question back. A plainer sentence that adds a claim is a worse defect than the manner it replaced. (Fiction is the exception, where invented detail is the job.)

## Seeds

Two greps, answering to different bars.

**Mechanical**, where every hit is a defect and the work is unfinished while one survives:

```
[—–]|[“”‘’]|\byou\b|\byour\b|\byourself\b|\bwe\b|\bour\b
```

**Hints**, where a hit is a sentence to read, not a verdict:

```
\bmeans\b|\bwould mean\b|\balone\b|\bactually\b|\bdeliberately\b|\bon purpose\b|\bsimply\b|\bjust\b|\bboth\b|\b(two|three|four|five|six|seven|eight|nine|ten|twelve|twenty)\b
```

Ordinary sentences trip the second one. "Leave the others alone" is not the intensifier "alone".

Grep seeds the work and does not bound it. The asides, closers, aphorisms, and stilted sentences carry no trigger word at all and surface only by reading.

## Lede

Open with the finding. The result, the answer, or the claim goes in the first sentence, and the context, method, and caveats that support it follow. Burying it under a scene-setting paragraph makes the reader assemble the point the writer already had.

The shape to watch is context, then answer, then a summary of the answer. Move the answer to the top and delete the summary. Reorder blocks and do not rewrite them. A block that needs a new sentence to survive the move has a dependency, and the dependency decides the order, not this rule. A closing paragraph that accurately restates what the document just said is still redundant, so end on the last concrete fact instead of a send-off.

This holds at every scale. Each section's first sentence carries that section's most important claim, and the **subject slot** rule below is the same instruction at clause scale.

Order is load-bearing when a block references something defined only in an earlier block. Test each candidate move against that before making it. Where it holds, the order stays and only the outcome moves to the front. A postmortem timeline, a proof, a migration's steps, and a tutorial pass this test as whole documents.

## Subject slot

Put the technical thing in the **subject slot** and let the verb say what is true of it. Gerund subjects ("Getting it wrong is harmless") and equivalence verbs ("Adding a machine means picking a name") both defer the fact by one clause. Name the thing, then state it.

Use the imperative for anything actionable. That is what retires second person: "install the binary on each machine", not "you have to install it yourself". Zero second person, stricter than Google's developer style guide, which endorses "you". Do not soften it back.

Elliptical forms are correct and often better: "No config file needed", "Only needed on a machine that pushes changes." A subjectless clipped sentence is a finished sentence, not a fragment to repair.

Vary the subject across consecutive sentences. Naming one noun at the head of four sentences running is stilted, and cycling synonyms for it ("the cache", "the store", "the layer") is a tell. Neither swap fixes the other. Restructure so the thing is named once and the sentences after it hang off that.

## Naming

Titles, headings, eyebrows, section names, and labels name the thing in precise professional register. No slang, casual shorthand, cute coinage, or clever wordplay. Sentence case, never title case.

| Clank | Plain |
|---|---|
| Getting Your Ducks in a Row | Prerequisites |
| The Magic Behind Caching | How the cache resolves a key |
| Setup, Speedrun Edition | Quick setup |
| Auth Stuff | Authentication |
| Two exclusions, both owned by DEVOPS-791 | Exclusions owned by DEVOPS-791 |

A heading followed by a one-line paragraph restating the heading is a warm-up. Delete the line and start on the content.

A heading or lead-in that counts what follows ("returns twenty lines", "both owned by", "the four refs above") makes the reader verify a number instead of reading the content. Drop the count where the set is printed right below it, and keep it where the body never shows the set.

## Tells

**Inflated significance.** is a testament, is a reminder, plays a vital role, crucial, pivotal, key moment, underscores its importance, reflects broader, marks a shift, turning point, evolving landscape, focal point, indelible mark, deeply rooted. State the fact and drop the claim about its importance.

**Promotional color.** vibrant, rich (figurative), profound, nestled, in the heart of, renowned, breathtaking, stunning, must-visit, groundbreaking (figurative), exemplifies, commitment to, natural beauty.

**Borrowed notability.** independent coverage, cited in national media outlets, written by a leading expert, maintains an active social media presence, a list of publication names offered as proof of importance. Keep a citation that carries real context, what was said and where, and drop the rest of the list.

**Machine vocabulary.** delve, tapestry, intricate, interplay, foster, garner, enhance, align with, landscape (abstract), realm, showcase, seamless, robust, leverage, harness, navigate (figurative), ensure, additionally, moreover, furthermore. These co-occur, so one hit is a reason to read the paragraph.

**Trailing participles.** A clause tacked on with highlighting, underscoring, emphasizing, ensuring, reflecting, symbolizing, contributing to, fostering, encompassing, or showcasing adds the shape of analysis and none of the content. Cut the clause, or promote it to a sentence that makes a claim.

**Manufactured rhythm.** Negative parallelism ("not just X, it's Y"), tailing negation ("no guessing" bolted to a sentence end), rule of three, false ranges ("from the Big Bang to dark matter"), runs of short declarative fragments staged for drama, aphorism formulas ("X is the language of Y", "the architecture of", "becomes a trap"), and paraprosdokian, the sentence whose tail reverses its head for surprise ("The retry logic handles every failure, except the one that matters"). Write the claim straight: "The retry logic does not handle timeouts."

**Formulaic sections.** A "Challenges" or "Future Outlook" section built from "Despite its X, Y faces several challenges" and closed with "Despite these challenges". State the specific problem and stop.

**Copula avoidance.** serves as, stands as, represents, marks, features, boasts, offers. Use is, are, and has.

**False depth and false candor.** Phrases that claim to cut through noise before restating an ordinary point: the real question is, at its core, in reality, what really matters, fundamentally, the deeper issue, the heart of the matter. Same move in a casual register, the theatrical pause before the reveal: "Honestly?", "Look,", "Here's the thing", "The thing is", "Let's be honest", "Real talk", used as a standalone hook. Delete the frame and keep the claim.

**Vagueness.** Weasel attribution (experts argue, observers have cited, industry reports, some critics, several sources). Hedge stacking (could potentially possibly). Filler (in order to, due to the fact that, at this point in time, it is important to note that, has the ability to). Generic upbeat conclusions. Speculative gap-fill about people (maintains a low profile, keeps personal details private, likely grew up, it is believed). Cutoff disclaimers (as of my last update, while specific details are limited). Name the real source, or say what is not known, or cut the sentence.

**Chatbot residue.** I hope this helps, Certainly, Great question, You're absolutely right, Would you like me to, Want me to, let me know, here is a. Sycophancy in any form.

**Signposting.** let's dive in, let's explore, let's break this down, here's what you need to know, without further ado, now let's look at. Do the thing instead of announcing it.

**Dashes.** No em dash or en dash. Replace each with a period, a comma, a colon, or parentheses, or restructure the sentence, and catch spaced dashes and double hyphens used the same way.

**Quote marks.** Straight quotes, not curly.

**Decoration.** No emoji. Bold marks a term being defined and nothing else, so a bulleted list of bolded headers with colons becomes a sentence.

**Hyphens.** Hyphenate a compound in attributive position (a high-quality report, a cross-functional team) and drop the hyphen when it follows the noun (the report is high quality, the team is cross functional). Uniform hyphenation in both positions is a tell, as are third-party, data-driven, real-time, end-to-end, and decision-making piled up together.

**Diff-anchored writing.** Describe the thing as it is, not as a change from what it was. "This function uses a hash map for O(1) lookups", not "This function was added to replace the previous iteration". Changelogs, release notes, and migration guides are version-scoped by nature and exempt.

## Calibration

None of these is clank on its own, and rewriting for them destroys real writing: perfect grammar, formal or academic vocabulary, a mixed casual and formal register, a single "however", correct complex formatting, a letter-style opening, unsourced claims, one short emphatic sentence, and "honestly" or "look" used mid-sentence, where the tell is the standalone theatrical opener and not the word. Clank comes in **clusters**. One em dash means nothing; em dashes plus rule of three plus "vibrant tapestry" plus a Conclusion section is a confession.

Preserve, and read as evidence of a real author: specific hard-to-fabricate detail, a real address, an odd quote, exact numbers and names, mixed feelings left unresolved, dated era-bound references, and uneven sentence length. A parenthetical carrying information stays. A parenthetical carrying attitude is purple and goes.

Never touch: quotations, titles, proper names, code blocks, frontmatter, data, link targets, identifiers, or a watched phrase being discussed rather than used. Skip vendored and generated files entirely.

## Modes

**Pasted text**, the default. Deliver the final rewrite, then a short list of what changed and why.

**File.** Rewrite the file in place so it holds only the final version. Humanize the prose and leave code blocks, frontmatter, data, and link targets alone. Report a summary of what changed rather than pasting the rewrite back.

**Embedded.** Another skill or agent is using this as one step of a larger job, such as a PR body, a commit message, or a doc. Output the final text alone. No draft, no audit, no summary.
