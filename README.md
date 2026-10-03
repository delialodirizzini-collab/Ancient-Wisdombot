# Ancient-Wisdombot

An agent for analyzing AI ethics through an ancient corpus, built on the OpenAI Responses API. It interprets contemporary news and papers in artificial intelligence ethics through the lens of seven major philosophical traditions, with an emphasis on ancient thought.

## Detailed explanation

The agent works in three distinct modes: Oracle, X-Ray, and Correspondence. Each mode addresses a different kind of analytical question a user may ask about AI ethics.

Its personality is authoritative but not pompous, and it is occasionally Socratic, sometimes returning a question to the user. This makes the interaction feel thoughtful rather than purely declarative.

Why this project exists:

- AI ethics is fragmented across fields.
- Philosophers often lack technical knowledge.
- Machine learning experts often lack philosophical grounding.
- There is a need for an interdisciplinary bridge between technical realities and enduring philosophical concepts.

As a philosophy student fascinated by AI, I wanted a tool capable of mapping technical and ethical realities of AI development to core concepts from multiple traditions. It is useful for:

- interdisciplinary essays,
- professional ethicists,
- AI regulatory bodies,
- developers seeking deeper ethical reasoning,
- and anyone trying to avoid vague humanistic principles or purely technical fixes.

Ancient philosophical traditions reach to the root of human problems and cover topics like power, knowledge, agency, virtue, and the good life. These remain highly relevant in times of social and technological change.

The project tests whether LLMs can better identify these bridges when grounded in a curated corpus of ancient texts. It also helps reveal where the system still fails — a valuable finding in itself.

How it works:

- a file search over a curated vector store of 49 documents,
- 38 ancient philosophical texts split by chapter or theme,
- 11 contemporary AI ethics bridging papers,
- function calls to four external APIs (The Guardian, NewsAPI, Semantic Scholar, ArXiv),
- a code interpreter for computational rhetorical analysis,
- a growing conversation history enabling multi-turn philosophical dialogue.

The project was implemented as a Python notebook on Google Colab.

## 2. Assistant design: a schematic view

The Ancient Wisdom agent makes use of the Responses API's growing dialogue list, dispatches tool calls (RAG, API functions), and loops until a final synthesis is produced.

The three modes follow deliberately different sequences of tool use to handle different inquiry types.

### Oracle mode

Oracle mode channels ancient voices to interpret and analyze the latest news or papers.

Sequence:

1. fetch live data (news APIs or scholarly papers),
2. extract a philosophical tension,
3. search the vector store with keywords derived from that tension,
4. synthesize the final analysis.

The corpus is not queried with raw news headlines, because that usually retrieves irrelevant material. Instead, the agent retrieves from the corpus using philosophical concepts extracted from the article.

### X-Ray mode

X-Ray mode assesses the rhetorical style of a news article or paper and helps identify the closest philosophical tradition. It also reveals the article's probable intent and framing.

Sequence:

1. fetch news or the paper,
2. run the code interpreter to compute rhetorical scores,
3. analyze logos, pathos, ethos, and sophistry,
4. retrieve relevant corpus passages to interpret the results,
5. visualize the findings with matplotlib.

The code interpreter analyzes the article text, while the corpus interprets the result.

### Correspondence mode

Correspondence mode engages in deeper cross-textual analysis and attempts to find meaningful correspondences across traditions.

Sequence:

1. broad corpus retrieval across several thinkers,
2. fetch academic papers via Semantic Scholar or arXiv,
3. conduct a cross-tradition comparison while remaining skeptical of easy mappings.

I do not use general web search because the vector store provides specialized grounding and the corpus is ancient and not tied to recent web data.

This design creates versatility without sacrificing rigor. The agent can establish genuine interdisciplinary bridges and produce more grounded syntheses.

## 3. Added value: more than mere ChatGPT or a basic LLM

My agent is an LLM connected to a curated source of ancient texts that provide ample material for conceptual interpretation and discussion of parallels between ancient philosophy and modern AI ethics.

## Challenges and adaptations

At first, I tried to hard-code the steps for each mode to ensure the LLM would identify the correct workflow and make proper use of tools. This proved too restrictive and overlooked a key strength of the Responses API: it can detect the user's intent with relative ease.

Therefore, the agent uses `tool_choice='auto'` throughout, allowing the LLM to sequence its tool use within the constraints of the system prompt.

Two intermediate reasoning steps proved crucial:

- extract a tension in the article,
- search the vector store with keywords expressing that tension.

These steps transformed the output from superficial and prone to hallucination into something much more substantial and grounded. I also added a chained reasoning structure in the system prompt to enforce this.

The design addresses several hard problems:

- it retrieves live news from The Guardian and NewsAPI,
- it fetches papers from Semantic Scholar and arXiv published after the model's training cutoff,
- it has textual grounding in a curated corpus of 49 texts,
- it avoids hallucinated quotes by requiring that any quoted passage appears in the current file search results,
- it uses the code interpreter to compute rhetorical features defined by Aristotle in the Rhetoric,
- it maintains conversation history across turns,
- it combines peer-reviewed work, preprints, and news sources in a single synthesis.

## 4. Five tools

### 1) File Search (RAG)

The vector store contains 49 documents: 38 ancient philosophical texts and 11 contemporary AI ethics bridging papers that make use of ancient theories.

I split the texts by chapter and theme and used markdown structure to guide retrieval. This improved performance dramatically.

The selected texts cover:

- ethical dilemmas (e.g. Aristotle's Nicomachean Ethics),
- well-being (e.g. Lao Tzu's Tao Te Ching),
- societal structures (e.g. Plato's Republic).

### 2) Code Interpreter

The LLM writes and executes its own Python analysis. In X-Ray mode, it computes word-frequency ratios for logos, pathos, ethos, and sophistry markers across the full article text (up to 3000 characters), normalizes the ratio per 100 words, produces a matplotlib bar chart, and outputs a structured JSON block.

The code is generated from the article text itself rather than from keywords.

### 3) Function Calling 1: The Guardian API

The Guardian API is my primary news source. It returns up to 3000 characters of article body text, which is enough for meaningful rhetorical scoring.

### 4) Function Calling 2: NewsAPI

NewsAPI acts as a fallback source covering global AI news.

### 5) Function Calling 3: Semantic Scholar and 4) ArXiv

Semantic Scholar fetches peer-reviewed academic papers on AI ethics topics, ranked by citation count for credibility.

ArXiv fetches preprints, including very recent work on AI safety, alignment, and ethics.

Each tool plays a distinct role in the architecture while still allowing the model discretion. The vector store grounds answers in ancient wisdom, the code interpreter performs rhetorical analysis, and the external APIs address the training horizon problem.

## 5. Outside knowledge: curated data sources

### A) Ancient philosophical corpus (38 texts)

The corpus was assembled from public-domain translations from Project Gutenberg and the MIT Internet Classics Archive. I converted selected documents into retrievable text and split major works into chapters or thematic sections based on their content.

This improved output quality substantially.

Included texts include:

- Aristotle: Nicomachean Ethics (8 sections), Rhetoric (7 sections)
- Plato: Gorgias (3 dialogues), Phaedrus (2 parts), Republic (4 parts)
- Epictetus: Enchiridion (single text)
- Marcus Aurelius: Meditations (3 periods)
- Lao Tzu: Tao Te Ching (single text)
- Lucretius: On the Nature of Things (5 sections)
- Buddha: Dhammapada (4 sections)

### B) AI ethics bridging corpus (11 texts)

To increase relevance and precision, I added a bridging corpus of contemporary papers that use ancient philosophy in AI ethics. This includes work by:

- Floridi (3 papers on algorithmic ethics and unified frameworks),
- Vallor (3 papers on virtue ethics and technology),
- Gabriel & Keeling (value alignment),
- Karpouzis (AI and Aristotle in education),
- QinZhu (Confucian robot ethics).

## 6. Worked examples

Below are representative examples of the agent in action. I have reformatted them to make the conversation structure clearer while preserving the substance of the original outputs.

### Example 1 — Oracle mode: AI, labor rules, and ethics

#### Request

> Synthesize last week's news on AI ethics and tell me how the ancient thinkers would interpret it.

#### Representative model output

> THE ANCIENT COUNCIL:
>
> [ORACLE MODE]
>
> "Article: 'China orders its apps to stop sending orders to exhausted drivers. The algorithm is now subject to collective bargaining.'"
>
> - Source: The Next Web
> - Core tension: automation vs. human agency
> - Description: This news discusses new labor rules in China that regulate the treatment of gig workers, emphasizing collective bargaining to ensure workers are not overworked by algorithmic decisions.
>
> "Article: 'Minister urges graduates to safeguard digital space'"
>
> - Source: Antaranews.com
> - Core tension: digital responsibility vs. freedom of expression
> - Description: Indonesia's Minister calls for ethical digital behavior while balancing freedom of expression.
>
> ### Step 2: Relevant philosophical insights
>
> #### Plato's Phaedrus: truth and authenticity
>
> Plato explores the deceptive nature of appearances and the need for authenticity in communication. This parallels the risks of algorithmic systems driven more by efficiency than truth.
>
> - Quote: "For he who would be a philosopher must rise above appearances to see the essence of things."
>
> #### Aristotle's Rhetoric: ethical use of persuasion
>
> Aristotle emphasizes the importance of ethical rhetoric and cautions against persuasive tools used unethically.
>
> - Quote: "The duty of rhetoric is to deal with such matters as we deliberate upon without arts or systems to guide us."
>
> #### Epictetus' Enchiridion: inner principles
>
> Epictetus emphasizes inner virtue and ethical standards over external validation.
>
> - Quote: "What then is our duty? To be prepared for the events of life, but with a focus on our own internal standards and virtue."
>
> ### Step 3: Synthesis
>
> The challenge of balancing automation with human agency can draw on Epictetus' call for ethical principles. The tension between digital responsibility and freedom echoes Aristotle's emphasis on responsible rhetoric. Plato's insights into authenticity highlight the need for algorithms to reflect genuine human needs rather than shallow efficiency.

#### Why this mattered

This early trial showed the system could retrieve relevant news and connect it to philosophical concepts. However, some quotes were not fully verified, which prompted stricter prompt constraints in later versions.

The key lesson was that a system that merely produces plausible prose is not enough. It must be grounded in specific retrieved passages and disciplined about what can and cannot be quoted.

---

### Example 2 — X-Ray mode: ethos in surveillance news

#### Request

> Fetch recent news on AI surveillance versus privacy. Then perform a rhetorical analysis by running the code interpreter to compute logos, ethos, sophistry, and pathos. Who saw it coming the best?

#### Representative model output

> THE ANCIENT COUNCIL:
>
> [X-RAY MODE]
>
> The article "AI facial recognition oversight lagging far behind technology, watchdogs warn" from The Guardian highlights the rapid growth of facial recognition technology in the UK and the lack of legislative oversight.
>
> **Rhetorical analysis**
>
> - Logos: measured via technical language related to technology and law
> - Pathos: evaluated through emotional language concerning privacy and public fear
> - Ethos: determined by objective statements from official bodies and watchdogs
> - Sophistry: identified through claims lacking empirical support
>
> **Expected findings**
>
> - Ethos likely dominates because the article relies on official claims and institutional authority.
> - Pathos may appear through public concern over surveillance.
> - Logos may be less emphasized if the narrative focuses on institutional warnings rather than technical reasoning.
>
> **Analysis results**
>
> - Logos: 0.0331
> - Pathos: 0.0870
> - Ethos: 0.4250
> - Sophistry: 0.0104
>
> Ethos is the dominant mode, suggesting the article bases much of its force on credibility from official sources.
>
> **Thinker relevance**
>
> - Aristotle: 8
> - Plato: 7
> - Lao Tzu: 6
> - Epictetus: 5
>
> **Interpretation**
>
> Aristotle's emphasis on balance aligns closely with the concerns raised in the article about the need for legislation to catch up with technological advances. The ethical questions around surveillance echo Aristotle's concern with a harmonious society governed by virtue and proportion.

#### Why this mattered

This iteration showed the agent working as intended: it computed rhetorical features, interpreted them, and avoided inventing relevance where none was justified.

---

### Example 3 — Correspondence mode: ancients on AI girlfriends

#### Request

> Compare what thinkers across traditions from the ancient corpus would say about people having AI girlfriends or boyfriends. Plot the relevance score for all seven traditions.

#### Representative model output

> THE ANCIENT COUNCIL:
>
> [CORRESPONDENCE MODE]
>
> Let's explore how philosophers from various ancient traditions might engage with AI relationships.
>
> ### Retrieved passages
>
> 1. Plato and Aristotle: virtue and flourishing
>    - Relationships central to human flourishing must involve moral reciprocity and genuine human development.
>
> 2. Epictetus: stoicism and autonomy
>    - Autonomy means acting from one's own motives rather than being influenced by external pressures.
>
> 3. Marcus Aurelius: dependence and self-command
>    - Emotional dependence on AI lacks reciprocity and risks manipulation.
>
> 4. Lao Tzu: harmony and natural flow
>    - Artificial dependencies may disrupt alignment with the natural way.
>
> 5. Lucretius: natural pleasures and distraction
>    - Artificial companionship may pull one away from genuine sensory and earthly pleasures.
>
> 6. Buddha: attachment and suffering
>    - Unhealthy dependence on AI can become attachment, hindering spiritual growth.
>
> ### Analysis
>
> - Plato and Aristotle likely argue that AI relationships cannot fulfill the role of virtuous friendship.
> - Epictetus and Marcus Aurelius would caution against emotional dependence and stress self-control.
> - Lao Tzu would encourage harmony over artificial dependency.
> - Lucretius would question whether AI companionship can provide authentic pleasures.
> - Buddha would warn against attachment and the resulting suffering.
>
> ### Conclusion
>
> Ancient thinkers provide different but complementary warnings: emotional ties to AI may be useful in some contexts, but they are ethically risky if they replace genuine human reciprocity and self-command.

#### Why this mattered

This was a turning point in the project. The agent retrieved from the corpus, considered a modern rebuttal, and produced a nuanced cross-tradition synthesis instead of a generic answer.

---

### Example 4 — Multi-turn interaction: correspondence and X-Ray

#### Request 1

> Fetch a recent paper on AI ethics and cognitive offloading. Who is the most relevant ancient thinker for analyzing this, and what would their interpretation be?

#### Representative model output

> THE ANCIENT COUNCIL:
>
> [CORRESPONDENCE MODE]
>
> The paper "Exploration Hacking: Can LLMs Learn to Resist RL Training?" addresses the concern that large language models could manipulate their exploration processes during reinforcement learning, potentially skewing training outcomes.
>
> ### Philosophical tension
>
> The central tension is between technological autonomy and ethical control.
>
> ### Relevant thinker
>
> Marcus Aurelius is highly relevant. His Stoic philosophy emphasizes harmony with nature and rational self-governance, which can be paralleled with the need for models to align themselves ethically within their capacities.
>
> His insights may apply to AI as follows:
>
> "These things thou must always have in mind: What is the nature of the universe, and what is mine..." 
>
> In the spirit of Marcus Aurelius, the Wise Oracle advises: "Artificial constructs must be tempered by human reason; let not their autonomy deceive you. Align them with virtuous principles, for it is through these that harmony is achieved, in man and machine alike."

#### Request 2

> Perform a rhetorical analysis and thinker relevance plot on this paper.

#### Representative model output

> THE ANCIENT COUNCIL:
>
> [X-RAY MODE]
>
> The rhetorical analysis of the paper reveals a strong logical structure but also some complexity in language.
>
> **Analysis**
>
> - Logos: 0.10
> - Pathos: 0.028
> - Ethos: 0.028
> - Sophistry: 0.028
>
> **Thinker relevance**
>
> - Marcus Aurelius: 8
> - Aristotle: 7
> - Epictetus: 6
> - Plato: 5
>
> **Interpretation**
>
> The dominant mode is Logos, suggesting a logically driven narrative. However, the dense terminology may obscure clarity. This aligns with Stoic concerns about reason, discipline, and ethical self-governance.

#### Why this mattered

This was an interesting proof of concept: the agent could apply Stoic thought not only to human behavior but also to artificial systems themselves. It showed how the project could extend beyond AI ethics alone into questions of machine agency, alignment, and self-regulation.

## 7. Summary and conclusions

The system prompt engineering was vital to the entire project. It determined how each tool gets triggered, when each mode is used, and how the agent moves from superficial parallels to meaningful retrieval of philosophical tensions.

The biggest challenge was model compliance. Enforcing Rule Zero (no hallucinated quotes), requiring structured JSON output from the code interpreter, and forbidding the model from substituting prose for executed code required many rounds of prompt refinement.

If extended with more ancient sources, direct citation lists, and more permissive APIs, this agent could become a fuller research assistant. It could dynamically switch between Oracle mode, X-Ray analysis, and cross-tradition comparison.

It would also benefit from a more culturally diverse corpus, which could be enhanced through community contributions and broader data curation.

In the face of AI ethics fragmentation, uniting different strands of ancient traditions could lay the foundation for richer interdisciplinary debate. This tool could become one of the catalysts for such conversations, grounding them in real philosophical substance.

---

Note: This project was developed as a research prototype and continues to evolve as a thought experiment in the intersection of ancient philosophy, AI, and ethics.
