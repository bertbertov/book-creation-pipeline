# HUMANIZER STYLE GUIDE
**Operational rewrite manual for AI-drafted manuscripts before KDP upload.**
For Albert Kamalov's non-fiction. One-strike warning is live. Treat every paragraph as if a reviewer at Amazon and a Pangram detector are reading it side-by-side.

Last updated: 2026-04-27. Sources cited inline; full source list at the end.

---

## (a) Executive summary — 5 do's and don'ts

1. **DO strip "AI-vocabulary" first, structure second, rhythm third.** Word-level tells (delve, tapestry, navigate, underscore) are the cheapest detection signal because they are token-frequency anomalies. Get rid of the words and you've already won 30% of the battle. The Max Planck study showed "delve," "robust," and "pivotal" usage spiked >50% in published writing post-ChatGPT — Amazon's classifiers are trained on that delta.
2. **DON'T trust any "humanizer" tool.** Pangram Labs (Amazon's likely vendor class) explicitly mentions defeating QuillBot, Undetectable.ai, and StealthGPT in their published benchmarks. Tool output has its own fingerprint. Round-trip translation through Russian (your native language) plus manual rewrite is more durable.
3. **DO disclose AI assistance in the KDP "Content Type" form.** Amazon penalizes non-disclosure, not disclosure. Every source agrees: disclosed AI-assisted content with quality editing publishes fine; undisclosed AI-generated content gets accounts terminated. (Inkfluence AI 2026 policy guide; Kindlepreneur.)
4. **DON'T leave em dashes, rule-of-three triples, or "not X — it's Y" parallelisms in the text.** These are the three most heavily weighted structural tells in current detector models. GPT-4o uses ~10× more em dashes than GPT-3.5 (Sean Goedecke), making em-dash density a near-perfect classifier feature.
5. **DO inject specifics, asymmetry, and friction.** Detectors flag *smoothness*. Real authors leave dates, names, contradictions, half-finished thoughts, and the occasional fragment. Burnt-out narrator voice — Albert's lane — is *defined* by friction. Lean into it.

---

## (b) 30+ specific AI tells with rewrites

Each entry: **(i) AI sentence → (ii) human rewrite → (iii) why detectors flag it.**

### Vocabulary giveaways

**1. Delve**
- AI: "Let's delve into the psychology of overwork."
- Human: "I want to look at why we work this way."
- Why: Highest-correlation single word in academic-paper detection studies. Max Planck Institute logged ~18,000 instances in published papers in four years post-ChatGPT — a 50%+ jump. (Entrepreneur, 2024.)

**2. Tapestry**
- AI: "The rich tapestry of modern self-help literature."
- Human: "Self-help books, 948 of them, since 1980."
- Why: Almost zero pre-ChatGPT prevalence in non-fiction prose. Specific count + date is anti-AI signal.

**3. Navigate (metaphorical)**
- AI: "How to navigate the complexities of burnout."
- Human: "How to live with burnout without quitting."
- Why: "Navigate" as transitive metaphor for non-water/non-spatial objects is a token AI loves; humans say "deal with," "handle," "get through."

**4. Intricate / multifaceted / nuanced**
- AI: "The intricate, multifaceted nature of self-help culture."
- Human: "Self-help is messier than that."
- Why: Three "abstract praise" adjectives stacked = AI signature. Humans usually pick one or zero.

**5. Pivotal / crucial / vital**
- AI: "This was a pivotal moment for the industry."
- Human: "After 2015, everything changed."
- Why: Significance inflation. Specific date does the same work without the puffery.

**6. Underscore / underscores**
- AI: "This underscores the importance of self-discipline."
- Human: "Discipline is the whole game."
- Why: Among the top-20 verb tells. Replace with "shows," "proves," or delete.

**7. Testament**
- AI: "His career is a testament to consistency."
- Human: "He didn't miss a deadline in 30 years."
- Why: Testament is biblical/elegiac. AI uses it for any persistent thing. Humans rarely say it.

**8. Paradigm / paradigm shift**
- AI: "A paradigm shift in how we think about productivity."
- Human: "We were wrong about productivity."
- Why: Pure cliché. Kuhn would weep.

**9. Embark on a journey / embark**
- AI: "Embark on a journey of self-discovery."
- Human: "Try this. See what happens."
- Why: Top-tier phrase tell. Listed in every "ban list" from Jodie Cook to Walter Writes.

**10. Holistic / synergy / encompass / foster / robust / comprehensive**
- AI: "A holistic, comprehensive approach that fosters robust outcomes."
- Human: "Do all three. Skip none."
- Why: Six abstract corporate words in one sentence is detector candy.

**11. Keen / keenly**
- AI: "She had a keen sense of timing."
- Human: "She knew when to shut up."
- Why: "Keen" is high-register and almost extinct in spoken English.

**12. Arguably / notably / interestingly**
- AI: "Arguably, this is the most important chapter."
- Human: "This is the most important chapter."
- Why: Hedging modifier that adds zero info. Mary Karr: voice speaks "with subjective curiosity," not academic hedge.

**13. Realm**
- AI: "In the realm of personal finance."
- Human: "When it comes to money."
- Why: Non-fantasy "realm" usage is ~95% AI now. Same with "domain," "sphere."

**14. "Shed light on"**
- AI: "Let me shed light on this misconception."
- Human: "Here's what's actually going on."
- Why: Cliché phrase, rule-of-thumb ban.

**15. "Serves as"**
- AI: "This chapter serves as a guide."
- Human: "This chapter is a guide." (Or just delete the meta-sentence.)
- Why: Copula avoidance — AI hates "is," uses "serves as / stands as / acts as." (Humanizer skill, blader/humanizer.)

**16. "At its core"**
- AI: "At its core, the book is about purpose."
- Human: "The book is about purpose."
- Why: Filler. "Persuasive authority trope" per agentkit's tropes reference.

**17. "It is worth noting"**
- AI: "It is worth noting that most diets fail."
- Human: "Most diets fail."
- Why: Pure stalling. If it's worth noting, just note it.

**18. "In today's fast-paced world"**
- AI: "In today's fast-paced world, attention is scarce."
- Human: "Nobody finishes books anymore."
- Why: Top-3 banned opener across every editor list (Content Beta, Jodie Cook, ContentBeta).

### Structure sins

**19. Em-dash addiction**
- AI: "He was tired — bone tired — the kind of tired that sleep doesn't fix — and he kept going."
- Human: "He was bone tired. Sleep wouldn't fix it. He kept going."
- Why: GPT-4o uses ~10× more em dashes than GPT-3.5 (Goedecke, 2024). Em-dash density is near-deterministic in detectors. **Hard rule: max 2 em dashes per chapter.**

**20. "Not just X, it's Y" / "It's not X. It's Y."**
- AI: "It's not just a job. It's a calling."
- Human: "Calling it a 'calling' is how you get exploited."
- Why: Single most-flagged sentence pattern (agentkit/ai-writing-tropes). Negative parallelism with em dash is the AI fingerprint.

**21. Rule-of-three abuse**
- AI: "Faster, cheaper, better." / "Mind, body, spirit." / "Read it, live it, share it."
- Human: Use 2 items or 4. Or one. "Faster and cheaper. Better is a lie."
- Why: AI defaults to tricolons because training data does. Three triples in a paragraph is a detector lock-in.

**22. Copula avoidance**
- AI: "The book represents a culmination of decades of work."
- Human: "The book is the end of thirty years of work."
- Why: AI swaps "is" for "represents/embodies/serves as/marks/stands as" because it sounds smarter. It doesn't.

**23. Synonym cycling (elegant variation)**
- AI: "The author... the writer... the essayist... the wordsmith..."
- Human: "The author. The author. The author." (Fowler called elegant variation a vice; AI inherited it.)
- Why: AI replaces repeated nouns with synonyms to avoid repetition. Real prose repeats key nouns deliberately.

**24. "From X to Y" false ranges**
- AI: "From CEOs to baristas, everyone's burned out."
- Human: "Everyone's burned out — including the CEO who fired me."
- Why: Two unrelated items framed as a spectrum. AI fakes scope this way constantly.

**25. Gerund-fragment litanies**
- AI: "Working late. Skipping meals. Forgetting birthdays."
- Human: "I worked late. I skipped meals. I missed my mother's birthday in 2014."
- Why: Subjectless gerund stacks are an AI rhythm signature. Humans use them rarely; when we do, we name what's being done.

**26. Anaphora abuse**
- AI: "They said it would help. They said it was urgent. They said we'd see results."
- Human: "They said a lot of things. None of them came true."
- Why: Three identical openings in a row reads as performance. Once is rhetorical; three times is AI.

**27. "The X? A Y." rhetorical question**
- AI: "The result? Catastrophe."
- Human: "It went badly."
- Why: Self-posed question + curt answer is GPT-4's favorite drama beat.

**28. Even paragraph length**
- AI: 3 paragraphs of 4 sentences each, all 18-22 words per sentence.
- Human: 6 sentences. Then a one-word paragraph. Then a 90-word paragraph that runs long because the thought ran long.
- Why: Burstiness. Variation in paragraph and sentence length is the #1 metric humans win on (GPTZero methodology). Target: stdev of sentence length > 8 words.

**29. Balanced clause endings**
- AI: "She wanted security; he wanted freedom; the marriage wanted neither."
- Human: "She wanted security. He didn't. They divorced in 2009."
- Why: Three clauses with parallel verbs is poetry-class workshop AI. Real writing doesn't balance like that without effort.

**30. Formulaic transitions**
- AI: "Furthermore, ... Moreover, ... Consequently, ... Notably, ..."
- Human: "And. But. So. Anyway."
- Why: Formal connectives are a top-5 tell. Drop or replace with conjunctions.

**31. Listicle compulsion**
- AI: Every sub-section becomes a numbered list of 3-5 items.
- Human: Prose with one list per chapter, max.
- Why: Bulleting is structural cowardice. Vegan-Badass voice is prose-driven; lists break it.

### Tone sins

**32. Significance inflation**
- AI: "This represents a profound shift in how we approach work."
- Human: "Office work is dying."
- Why: AI inflates because it's been RLHF'd to sound impressive. Burnt-out narrator does the opposite — strips importance.

**33. Promotional fluff**
- AI: "This groundbreaking, must-read book offers transformative insights."
- Human: "I wrote this because I wish I'd had it in 2018."
- Why: "Groundbreaking / must-read / transformative" are jacket-copy AI defaults.

**34. Generic positive conclusions**
- AI: "The future looks bright. Exciting times ahead."
- Human: "Most of you won't do this. The few who do will be okay."
- Why: AI ends every section uplifting. Vegan-Badass voice ends honest, sometimes flat, sometimes dark.

**35. Sycophancy / chatbot artifacts**
- AI: "Great question! That's an excellent point. I hope this helps."
- Human: [Delete entirely.]
- Why: Conversational chatbot leakage. Almost every flagged AI book has at least one of these surviving from a draft prompt.

**36. Excessive hedging**
- AI: "It could potentially be argued that this might somewhat affect outcomes."
- Human: "This affects outcomes."
- Why: Stacked hedges (could / potentially / might / somewhat) signal model uncertainty leaking into prose.

### Rhythm tells

**37. Sentence-length variance < 6 words**
- AI default: every sentence between 14-22 words.
- Human: 4 words. Then forty-three words that stretch and curl through three clauses, doubling back on themselves before they land. Then six. Then one. Burnt out.
- Why: Burstiness is the second pillar of GPTZero detection (humans 0.6–1.2; GPT clusters 0.2–0.4).

**38. Paragraph-length uniformity**
- Same fix. Vary 1-sentence paragraphs with 8-sentence paragraphs.

---

## (c) Detection mechanics — what perplexity and burstiness actually measure

**Perplexity** = how surprised a language model is by the next word. Formally: 2^H where H is the cross-entropy of the text under the model. Low perplexity = predictable = AI-shaped. High perplexity = surprising word choices = human-shaped.

**Target ranges (per GPTZero's published methodology):**
- Human writing average: ~80–100 perplexity units.
- GPT-4 output average: ~20–30.
- A perplexity above ~85 reads as more-likely-human.
*(Source: GPTZero methodology page; Hastewire 2024 deep-dive.)*

**Burstiness** = standard deviation of perplexity *across sentences in a document*. Humans mix predictable-and-surprising sentences; AI is uniformly predictable.
- Human burstiness: 0.6–1.2 (per Tian et al.'s GPTZero methodology).
- GPT output: 0.2–0.4.
*(Source: GPTZero; tryleap.ai burstiness primer.)*

**What this means operationally:**
- A paragraph where every sentence sits between 14-22 words and uses high-frequency vocabulary lands at perplexity ~25, burstiness ~0.3 → flagged.
- A paragraph with one 4-word sentence, one 40-word sentence, and one off-register noun (e.g., "Pravilo," "Bishkek," "ortholinear") lands at perplexity ~90, burstiness ~1.0 → reads human.

**The Krishna et al. (2023) caveat (NeurIPS, arXiv:2303.13408):** Paraphrasing alone drops detection accuracy of DetectGPT from 70.3% to 4.6% at 1% FPR. *But* their proposed retrieval-defense (find the original source AI text in a corpus) is what Amazon-class detectors are now using — meaning paraphrasing surface tokens isn't enough. **The text needs new ideas, not just new words.**

**The Pangram critique:** Pangram Labs (industry leader, ~1-in-10,000 false positive rate) abandoned perplexity/burstiness for deep-learning classifiers trained on millions of paired (AI, human) samples. They specifically detect outputs of QuillBot, Undetectable.ai, StealthGPT *as humanizers*. **Implication: anything that's been near a humanizer tool may be a worse signal than raw GPT output.**

---

## (d) Per-paragraph rewrite checklist (10 steps)

Apply to every paragraph of every chapter. Don't skip steps.

1. **Read it aloud.** If you wouldn't say it talking to a friend at a bar, rewrite it.
2. **Word-bank pass.** Search the paragraph for any word in section (e). If found: replace or delete.
3. **Em-dash count.** Max one per paragraph. Replace others with periods, commas, or parentheses.
4. **Tricolon hunt.** Find any "X, Y, and Z" rhythm. Cut to two items or extend to four. Break the cadence.
5. **"Not X — it's Y" hunt.** Search the paragraph for the negative-parallelism pattern. Rewrite as direct claim.
6. **Significance check.** Any sentence claiming the topic is "important / pivotal / crucial / profound"? Delete the claim. Replace with a specific that *shows* importance (a date, a name, a number).
7. **Specificity injection.** Every paragraph should contain at least one of: a date, a name, a place, a number, or a sensory detail. AI defaults to abstract; humans default to concrete.
8. **Variance check.** Sentence lengths in this paragraph should range from <8 words to >25 words. If they don't, rewrite one short and one long.
9. **First-person friction.** Insert a self-correction, a contradiction, a change of mind, or a moment of reader-direct address (*"You're going to disagree with this and that's fine."*). One per page minimum for Vegan-Badass voice.
10. **Re-read against VEGAN_BADASS_VOICE.md.** Burnt-out narrator. Second person. Hard truths without contempt. If the paragraph reads neutral or upbeat, it's wrong.

---

## (e) Banned phrase + word list

**Hard ban (do not appear in any final manuscript):**

*Single words:* delve, leverage, tapestry, navigate (metaphorical), intricate, multifaceted, nuanced, pivotal, underscore, underscores, testament, paradigm, embark, holistic, synergy, encompass, foster, robust, comprehensive, keen, arguably, notably, realm, domain (metaphorical), sphere (metaphorical), elucidate, illuminate (metaphorical), delineate, garner, harness, hone, unveil, unleash, unlock, supercharge, turbocharge, skyrocket, revolutionize, transformative, groundbreaking, must-read, cutting-edge, game-changer, paramount, profound, vibrant, breathtaking, stunning, captivate, compelling, scrappy, nimble, cornerstone, bombard, formidable, indelible, enduring, vital, crucial, essential (as filler), utilize, interplay, captivate.

*Two-to-five-word phrases:* "shed light on," "serves as," "stands as," "marks a shift," "at its core," "in essence," "in the realm of," "the heart of the matter," "the deeper issue," "what really matters," "it is worth noting," "it is important to note," "in today's fast-paced world," "in a world where," "in the digital age," "embark on a journey," "let's dive in," "let's explore," "without further ado," "here's the kicker," "here's the deal," "at the end of the day," "in conclusion," "to sum up," "in summary," "the bottom line is," "to say the least," "needless to say," "a testament to," "a tapestry of," "rich tapestry," "navigate the complexities," "navigate the landscape," "in the ever-evolving landscape," "play a pivotal role," "play a crucial role," "play a vital role," "the evolving landscape of," "stay tuned," "imagine if," "what if," "have you ever wondered," "isn't it obvious that," "first and foremost."

*Sentence patterns:* "It's not just X, it's Y." "Not X. Not Y. Just Z." "Not because X, but because Y." "The X? Y." "It's not about X, it's about Y." "On one hand... on the other hand..." "Not only X but also Y."

*Transitions:* furthermore, moreover, consequently, notably (as opener), importantly (as opener), interestingly (as opener), indeed, in fact (as opener), as a result.

*Conclusion clichés:* "All in all," "When all is said and done," "At the end of the day," "Looking ahead," "The future looks bright," "Exciting times ahead," "A major step in the right direction."

*Filler phrases — replace with the right column:*
- "in order to" → "to"
- "due to the fact that" → "because"
- "at this point in time" → "now"
- "in the event that" → "if"
- "has the ability to" → "can"
- "a wide variety of" → "many" or list them
- "a number of" → name the number

*Hedge stack:* quite, rather, somewhat, possibly, potentially, presumably, seemingly, essentially, basically. Use **at most one** of these per chapter.

*Promotional adjectives (in author's own voice):* groundbreaking, revolutionary, transformative, must-read, essential, life-changing, paradigm-shifting, game-changing, cutting-edge, stunning, breathtaking, vibrant, nestled.

*Chatbot artifacts (search and destroy):* "I hope this helps," "Of course!," "Certainly!," "Great question!," "You're absolutely right!," "Let me know if," "as of [date]," "as of my last training," "while specific details are limited."

*Curly quotes / emoji decoration in body text:* gone.

---

## (f) What humans do that AI doesn't reproduce — 25 patterns

These are the moves Albert needs to *add*, not just remove AI tells.

1. **Asymmetric rhythm.** A 3-word sentence next to a 50-word sentence. AI flattens; humans whiplash.
2. **Mid-paragraph topic shift.** Start about deadlines, end with a memory of your father. AI stays on-topic; minds drift.
3. **Embodied detail.** "My right shoulder went numb at hour eleven." AI has no body.
4. **Sentence fragments for emphasis.** Three words. Period. Move on. (Chuck Wendig's whole thing. Lamott does this.)
5. **Self-correction.** "I told myself it was discipline. It wasn't. It was fear." AI rarely walks back a claim.
6. **Profanity for punctuation.** One "fuck" per chapter, used surgically, signals human. (Mary Karr's *Liars' Club* register; Havrilesky's *Ask Polly*.)
7. **Specific dates over rounded approximations.** Not "in the late 2010s" — "October 2018." Not "thousands of books" — "948 books." (Already in Albert's voice spec.)
8. **Specific names over generic actors.** Not "an executive at a tech firm" — "Andy Grove at Intel." Not "researchers" — "Anders Ericsson, in 1993, in Berlin."
9. **Direct second-person address.** "You're going to want to skip this chapter. Don't." (Karr's *Cherry* ~50% in second person; Havrilesky's *Ask Polly* default register.)
10. **Idiosyncratic word choice.** A word the author always uses — Albert's word for an offhand reader: *fellow human*, or his Russianism *honestly*. Repeat it. Make it a tic. AI flattens tics.
11. **Off-register vocabulary.** Albert's Russian words (Pravilo, ruble, Bishkek). Old slang. Trade jargon (KDP, ACX, RTF, em-dash itself). These spike perplexity instantly.
12. **Calling out the genre.** "This is the part where self-help books tell you to make a vision board. I'm not going to." Self-aware genre-breaking is highly human.
13. **Anti-uplift.** Ending a section flat, ironic, or grim. AI ends optimistic by default.
14. **Embedded contradiction.** "I quit caffeine. Mostly. I had two coffees yesterday." Real life is messy.
15. **Number-with-context.** Not "many" — "seventeen, including the one I bought twice." A number naked is just data; with context it's voice.
16. **Time-stamp the writing.** "I'm typing this on the morning of October 14th. The light is wrong." Self-locating in time is anti-AI.
17. **Refer to the previous book / earlier chapter / an old draft.** "I said in *I Read 948 Business Books* that..." AI doesn't have prior work.
18. **Quote yourself disagreeing with yourself.** "Past me wrote: 'Just lift heavy.' Past me was an idiot."
19. **Trail off mid-sentence — once.** Not as a tic, but at one moment of high feeling. "And then she — anyway."
20. **Use *because* without finishing the reason.** "I stopped lifting. Because." (One per book, max. High-impact.)
21. **Single-word paragraphs.** "No." "Stop." "Wrong." Detector-friendly because it nukes burstiness flatness.
22. **Long, dependent-clause-stacked sentence with a comic landing.** Anne Lamott does this constantly. Build pressure with subordinate clauses, release with a deflating final beat.
23. **Direct insult of a category, not a person.** "Most productivity bloggers have never produced anything." Not personal, but pointed. Havrilesky-class.
24. **Brand or product names spelled out.** Not "a video conferencing app" — "Zoom." Not "a fitness app" — "Strava." AI was RLHF'd to genericize; humans aren't.
25. **A piece of advice that contradicts the chapter title.** Earned, not flippant.

---

## (g) Round-trip translation as anti-detection

**Theory.** Krishna et al. (NeurIPS 2023, *Paraphrasing evades detectors of AI-generated text*) showed paraphrasing alone drops DetectGPT accuracy from 70.3% to 4.6% at 1% FPR. The mechanism: token-level statistical fingerprints don't survive a semantic round trip. Round-trip translation (RTT) is paraphrasing's stronger cousin because the intermediate language imposes its own grammatical constraints, breaking the source token distribution more thoroughly than a same-language paraphrase.

**Workflow for Albert (EN → RU → EN):**

1. **Translate EN draft → RU.** Use DeepL Pro (best for literary register), not Google Translate. DeepL preserves voice; Google Translate flattens.
2. **In Russian, manually edit for register.** This is the critical step. Albert is bilingual. Read the Russian draft and adjust for the burnt-out-narrator register *in Russian* — fix anything that sounds stilted, clinical, or AI-translated. Roughly 10-15 minutes per chapter.
3. **Translate RU → EN.** DeepL again. The output will read slightly off, slightly Slavic — *which is good*, because that off-register lands as high perplexity.
4. **Manually rewrite the English back-translation against this style guide.** Apply section (d)'s 10-step checklist. The RTT step has scrambled token distribution; this step adds voice and fixes RTT artifacts.

**What gets lost:**
- **Idioms.** RTT mangles "down to the wire," "burn the candle at both ends," etc. Manually re-insert any idioms you wanted.
- **Wordplay and puns.** Always lost. Rewrite in step 4.
- **Pronoun-antecedent chains.** Russian uses gender; English doesn't. RTT can confuse "she/it/they." Read carefully for this.
- **Em-dash structure.** Russian uses dashes differently; RTT often drops them. *This is a feature, not a bug* — keep them dropped.
- **Cultural references.** "Like a Black Friday sale" might come back as "like a holiday sale." Re-insert specifics.

**What to manually check after:**
- **Numbers and dates.** RTT sometimes rounds ("948" → "almost 1,000"). Restore exact figures.
- **Proper nouns.** Names of authors, brands, places. Verify spelling.
- **Quotations.** Anything in quotes must be re-pasted from the original — RTT will paraphrase quoted material.
- **Voice anchors.** Compare a paragraph against the SAMPLE_CH1_HARD_WORK_LIE.md anchor. If the back-translation went too neutral, rewrite for register.

**Why this beats commercial humanizers:** Pangram explicitly trains against QuillBot, Undetectable.ai, StealthGPT outputs. RTT through DeepL produces a token distribution Pangram doesn't have a labeled-class for. Plus, *manual rewrite is the only step that adds new ideas* — the retrieval defense (Krishna et al. §5) catches paraphrases that preserve the original's idea structure.

---

## (h) Voice anchors

The Vegan-Badass voice — burnt-out narrator, second-person, hard truths without contempt — has direct lineage in published nonfiction. Use these as register references when a paragraph reads off:

1. **Mary Karr — *The Liars' Club*, *Cherry*, *The Art of Memoir*.** Karr is the patron saint of voice in memoir. *Cherry* is ~50% in second person — written that way because, as Karr explained to the *Paris Review*, she "didn't have a self to be inside" and second person gave her detachment. Karr's voice: "poetic yet profane; rich yet gravelly; lyrical yet chock-full of Texan idioms" — register profile to copy.

2. **Anne Lamott — *Bird by Bird*, *Operating Instructions*.** Lamott invented the modern self-deprecating-narrator nonfiction voice. Specifically her use of: short blunt sentences, sudden religious or moral pivots, comic exaggeration, willingness to be the fool of her own essay. Her "shitty first drafts" chapter is the structural template for chapter-as-confession.

3. **Heather Havrilesky — *How to Be a Person in the World*, *Ask Polly*.** Havrilesky's column is the working model for second-person Vegan-Badass voice. She delivers hard truths with grace. Her trick: build empathy in the first paragraph, drop the hammer in the third, end with practical advice rather than uplift. Albert's "Three things to do, today, and not a fourth" closer is structurally Havrilesky-shaped.

4. **(Bonus) Caitlin Doughty — *Smoke Gets in Your Eyes*.** Mordant wit, gallows humor, professional burnout register. Useful when chapters get dark.

5. **(Bonus) David Foster Wallace — *Consider the Lobster*, *A Supposedly Fun Thing*.** Long sentence + footnote voice. Use sparingly; DFW's tics are themselves now AI-imitated.

**Operational rule:** if you're rewriting a paragraph and you don't know what register it should land at, open a Karr or Havrilesky page at random and read 2 minutes before continuing.

---

## (i) What KDP and other publishers' detectors specifically catch

**Amazon KDP (2026 policy and practice):**
- Amazon distinguishes **AI-generated** (must disclose) from **AI-assisted** (no disclosure required). The disclosure dropdown is part of the Content Type page during upload. (KDP Help: G200672390.)
- Detection stack: "automated detection (analyzing writing patterns, metadata, and submission velocity) and human review." (Inkfluence AI 2026 policy page.)
- **High-velocity publishing is the #1 trigger.** Multiple short books in days = automatic flag. Long-form nonfiction (>10,000 words) at <1 book/month is the safe envelope.
- Amazon also looks for **hallucinations** — fabricated citations, fake studies, made-up quotes. Verify every cited fact in Albert's drafts. A made-up Stanford study cited in your draft is the single fastest path to termination.
- **Disclosure does not affect ranking or visibility.** This is officially confirmed by Amazon and re-confirmed in every 2026 publishing-advisor source.
- **One-strike account state (Albert's situation):** treat the next upload like an audit. Disclose AI assistance honestly, keep word count high (>25,000 for fiction, >10,000 for non-fiction), avoid any rapid-publishing burst, and do not upload more than one book per 14 days.

**Apple Books:**
- Less public on detection. Their digital narration policy (Apple Books Digital Narration) requires AI-narration disclosure for audio. Text-side detection is opaque but presumed similar to KDP.

**Google Play Books:**
- Officially supports AI-narrated audiobooks. Text detection appears more permissive than Amazon's. Still: disclose.

**Findaway Voices (now INaudio / Spotify-owned):**
- Allows AI narration with disclosure. The 2024 Terms-of-Use blowup (Writer Beware coverage) was about training rights, not detection. They are not currently aggressive on text-side AI detection — they're a distributor, not a publisher.
- **Reminder for Albert:** ACX is not available in UAE — Findaway Voices is the audio path. AI-narrated audiobooks must be disclosed; AM_michael is currently a Kokoro voice, which is non-cloned and disclosable as "AI-generated narration."

**Detector vendors used (publicly known or rumored) by major publishers:**
- **Pangram Labs:** Industry leader, ~1-in-10,000 false positive rate, deep-learning classifier (not perplexity/burstiness). Used by publishing/media (Pangram's own use-case page). Highly likely Amazon vendor.
- **Originality.ai:** Trained on GPT-3/3.5/4. Score-based output. Public-facing; many self-publishers self-test with it before upload (target <20% AI score).
- **GPTZero:** Older perplexity/burstiness model. Mostly used in academia.
- **Winston AI:** Self-publisher-favored pre-screening tool. Target <20-30% AI score before upload.

**Self-screening protocol before any KDP upload (recommended):**
1. Run the manuscript through Originality.ai. Target: <20% AI.
2. Run through GPTZero. Target: >85 perplexity, >0.7 burstiness.
3. Run a 3-sample manual perplexity test: paste 3 random paragraphs into GPTZero. If any single paragraph scores <40 perplexity, rewrite that paragraph.
4. Verify all factual citations exist (Google Scholar each one).

---

## (j) Sources cited

**KDP / publisher detection policy:**
- Inkfluence AI 2026 KDP AI Policy: https://www.inkfluenceai.com/blog/amazon-kdp-ai-disclosure-policy-2026
- Amazon KDP Content Guidelines: https://kdp.amazon.com/en_US/help/topic/G200672390
- Kindlepreneur — KDP suspension causes & appeals: https://kindlepreneur.com/amazon-kdp-account-suspension/
- Barker Books — Amazon KDP News 2026: https://barkerbooks.com/amazon-kdp-news/
- Writer Beware on Findaway Voices: https://writerbeware.blog/2024/02/19/outrage-over-new-terms-of-use-at-findaway-voices-forces-change/
- Pangram Labs publishing use case: https://www.pangram.com/use-cases/publishing

**AI detection mechanics (perplexity, burstiness, classifiers):**
- GPTZero — Perplexity & Burstiness primer: https://gptzero.me/news/perplexity-and-burstiness-what-is-it/
- GPTZero — How AI detectors work: https://gptzero.me/news/how-ai-detectors-work/
- Pangram Labs — Why perplexity & burstiness fail: https://www.pangram.com/blog/why-perplexity-and-burstiness-fail-to-detect-ai
- tryleap.ai — Perplexity vs Burstiness: https://www.tryleap.ai/learn/perplexity-vs-burstiness
- Hastewire — How AI detectors calculate perplexity & burstiness: https://hastewire.com/blog/how-ai-detectors-calculate-perplexity-and-burstiness

**Academic papers:**
- Krishna, Song, Karthikeyan, Wieting, Iyyer (NeurIPS 2023) — *Paraphrasing evades detectors of AI-generated text, but retrieval is an effective defense*: https://arxiv.org/abs/2303.13408 / https://github.com/martiansideofthemoon/ai-detection-paraphrases
- *Diversity Boosts AI-Generated Text Detection* (2024): https://arxiv.org/pdf/2509.18880
- *Detecting AI-Generated Text with Pre-Trained Models using Linguistic Features* (ICON 2024): https://aclanthology.org/2024.icon-1.21.pdf
- *Mitigating Paraphrase Attacks on Machine-Text Detectors via Paraphrase Inversion* (2024): https://arxiv.org/html/2410.21637
- *TempParaphraser* (EMNLP 2025): https://aclanthology.org/2025.emnlp-main.1607.pdf

**AI-tell vocabulary and structure references:**
- Sean Goedecke — *Why do AI models use so many em-dashes*: https://www.seangoedecke.com/em-dashes/
- Jodie Cook — Ban list: https://www.jodiecook.com/ban-list/
- Content Beta — 300+ AI words list (2026): https://www.contentbeta.com/blog/list-of-words-overused-by-ai/
- Walter Writes AI — Most common ChatGPT words 2026: https://walterwrites.ai/most-common-chatgpt-words-to-avoid/
- Ivana Ćirković — Dead giveaway AI words: https://ivanacirkovic.com/en/tipstricks-en/the-ultimate-list-of-dead-giveaway-ai-words-in-the-content/
- AI Phrase Finder — Full list: https://aiphrasefinder.com/words-that-identify-ai/
- Entrepreneur — 'Delve' giveaway: https://www.entrepreneur.com/business-news/delve-is-a-giveaway-chatgpt-wrote-that-email-expert/472566
- Ruben Hassid — *Ban.* (Substack): https://ruben.substack.com/p/delve

**GitHub humanizer tooling (operational rules, AI-trope catalogs):**
- agentkit/skills/ai-writing-tropes/references/sentence-structure.md: https://github.com/joshuadavidthomas/agentkit/blob/main/skills/ai-writing-tropes/references/sentence-structure.md
- blader/humanizer SKILL.md: https://github.com/blader/humanizer/blob/main/SKILL.md

**Voice anchors:**
- Mary Karr — *Paris Review* interview, Art of Memoir No. 1: https://www.theparisreview.org/interviews/5992/the-art-of-memoir-no-1-mary-karr
- Anne Lamott — TED Ideas, *13 writing tips*: https://ideas.ted.com/14-writing-tips-from-beloved-teacher-anne-lamott/
- Heather Havrilesky — *How to Be a Person in the World*: https://www.penguinrandomhouse.com/books/252018/how-to-be-a-person-in-the-world-by-heather-havrilesky/
- Caitlin Doughty — *Smoke Gets in Your Eyes*: https://www.audible.com/pd/Smoke-Gets-in-Your-Eyes-Audiobook/B00NP724GS

**False-positive evidence:**
- Stanford TOEFL false-positive study (61% of TOEFL essays flagged as AI by ≥1 detector): summarized in Skyline Academic — https://skylineacademic.com/blog/ai-detection-false-positives/
- Originality.ai false positive analysis: https://thehumanizeai.pro/articles/can-ai-detection-be-wrong-false-positives

---

**Tied-back to Albert's voice spec:** every recommendation here is consistent with `_shared/VEGAN_BADASS_VOICE.md` (burnt-out narrator, second-person address, hard truths without contempt, "Three things to do, today, and not a fourth" closer). Voice anchor: `948_Business_Books/SAMPLE_CH1_HARD_WORK_LIE.md`. When in doubt, reread the anchor before rewriting.

**End of guide.**
