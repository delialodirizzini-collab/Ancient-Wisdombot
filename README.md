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

![Project diagram](diagram%20ancient%20bot.png)

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

### B) AI Ethics Bridging Corpus (11 PDFs) 
Then, to increase the agent’s output relevance and precision, I added a Bridging 
Corpus with 11 contemporary examples of papers using ancient philosophy in AI 
Ethics. This includes : 
9 - 
Contemporary AI ethics papers by Floridi (3 papers on algorithmic ethics 
and unified frameworks), Vallor (3 papers on virtue ethics and technology), 
Gabriel & Keeling (on value alignment), Karpouzis (AI and Aristotle in 
education), QinZhu (Confucian robot ethics). 
# 5. Worked Examples
Request : Synthesize last week’s news on AI Ethics and tell me how the Ancient thinkers 
would interpret it. --- 
OUTPUTS  
THE ANCIENT COUNCIL: 

[ORACLE MODE] 
“Article: "China orders its apps to stop sending orders to exhausted drivers. The algorithm 
is now subject to collective bargaining."**  - Source: The Next Web  - Core Tension: Automation vs. Human Agency  - Description: This news discusses new labor rules in China that regulate the treatment of 
gig workers, emphasizing collective bargaining to ensure workers are not overworked by 
algorithmic decisions.  
2. **Article: "Minister urges graduates to safeguard digital space"**  - Source: Antaranews.com  - Core Tension: Digital Responsibility vs. Freedom of Expression  - Description: Indonesia's Minister calls for young graduates to protect the digital space, 
reflecting a tension between maintaining ethical standards online and upholding freedom 
of expression.  
### Step 2: Relevant Philosophical Insights  
#### Plato's Phaedrus: Truth and Authenticity  - Plato explores themes of true discourse and the deceptive nature of appearances, 
suggesting the need for authenticity in communication. This ties into the need for 
algorithms, often driven more by efficiency than truth, to uphold human authenticity.  - Quote: "For he who would be a philosopher must rise above appearances to see the 
essence of things."  
#### Aristotle's Rhetoric: Ethical Use of Persuasion  
10 - Aristotle discusses the importance of ethical rhetoric, recognizing the potential harm of 
persuasive tools used unethically—parallels with the impact of AI-driven algorithms on 
gig workers’ rights.  - Quote: "The duty of rhetoric is to deal with such matters as we deliberate upon without 
arts or systems to guide us, in matters that belong within the range of the possible."  
#### Epictetus' Enchiridion: Inner Principles  - Emphasizes the importance of focusing on inner virtues over external validations, 
applicable here in the context of relying on firm ethical principles when designing AI 
systems.  - Quote: "What then is our duty? To be prepared for the events of life, but with a focus on 
our own internal standards and virtue."  
### Step 3: Synthesis  
In light of these insights:  - The challenge of balancing automation with human agency can draw on **Epictetus'** 
call for focus on ethical principles, suggesting that both workers and developers prioritize 
ethical standards over external demands.  - The tension between digital responsibility and freedom echoes **Aristotle's** emphasis 
on responsible rhetoric—AI systems should aim for ethical persuasion.  - **Plato's** insights into authenticity highlight the importance of ensuring algorithms 
reflect and support genuine human needs rather than shallow efficiency.  
This philosophical synthesis suggests that engaging deeply with ancient ethical principles 
provides essential guidance for managing current issues within AI ethics effectively.” 
In this early trial, the assistant successfully retrieved a relevant piece of news and 
quoted from relevant ancient passages. However, some of the quotes are not 100 % 
verified (Plato’s quote), which prompted me to be stricter in the System Prompt.  
When asked the same question, ChatGPT gives plausible answer but fabricates quotes, 
retrieves no philosophical tension and overgeneralizes. When asked from which texts 
it has quoted, it retrieves other works than the relevant ones. 
# Example 2 — X-RAY mode: Ethos in Surveillance news. 
Request : fetch recent news on AI surveillance vs privacy. Then perform a rhetorical 
analysis on it by running your code interpreter to compute the logos, ethos, 
sophistry, pathos etc. Who saw it coming the best? 

Rendering charts from prose scores... 
![Project diagram](fig%202%20ancient%20bot.png)
Figure 2 X-Ray analysis over a Guardian article with Ethos as main value 

THE ANCIENT COUNCIL: 

[X-RAY MODE] 
The article "AI facial recognition oversight lagging far behind technology, watchdogs 
warn" from The Guardian (2026-05-03) highlights the rapid growth of facial recognition 
technology in the UK and the lack of corresponding legislative oversight. Notably, the 
Metropolitan police have nearly doubled the number of faces scanned in London over the 
past year. 
**Rhetorical Analysis:** 
1. **Linguistic Features:**  - **Logos:** Measured by the use of technical terms related to technology and legislation. - **Pathos:** Evaluated through sentiment analysis, looking for emotional language  concerning public fears around privacy. - **Ethos:** Determined by the presence of objective statements about oversight and 
legal frameworks. - **Sophistry:** Identified by assertions and claims lacking empirical support. 
2. **Expected Findings:** - Likely to find a higher score in Ethos due to the article's reliance on official statements 
from biometrics commissioners and watchdogs. - Pathos may appear through discussion of public fear of surveillance.
Note: This project was developed as a research prototype and continues to evolve as a thought experiment in the intersection of ancient philosophy, AI, and ethics.
- Logos could be less emphasized if there is a focus on subjective claims. 
**Analysis Results:** - Logos: 0.0331 - Pathos: 0.0870 - Ethos: 0.4250 - Sophistry: 0.0104 
Ethos is the dominant mode, suggesting the article bases its arguments largely on 
credibility from official sources. 
**Thinker Relevance:** - Aristotle: 8 (focus on balance and reality) - Plato: 7 - Lao Tzu: 6 - Epictetus: 5 - Others: Lower relevance 
**Interpretation:** 
Aristotle's emphasis on maintaining a balance aligns closely with the concerns raised in 
the article about the need for legislation to catch up with technological advances. The 
ethical considerations in monitoring technologies like facial recognition echo Aristotle's 
focus on achieving a harmonious society through virtuous regulations. 
Overall, Aristotle's caution regarding the balance between progress and ethical oversight 
remains highly relevant in addressing the highlighted tension between technology and 
privacy.

Here the agent responded as it was supposed to and computed a sophisticated 
analysis of the linguistic features. It also avoided inventing relevance where 
there was none for other thinkers. 
Example 3 — Correspondance mode: Ancients on AI 
Girl/boyfriends 
Request :  compare what thinkers across tradition from the ancient corpus would 
say on people having AI girl/boyfriends. Plot relevance score for all 7  
Corpus searched (1)
![Project diagram](fig%203%20ancient%20bot.png)
THE ANCIENT COUNCIL: 
[CORRESPONDENCE MODE] 
Let's explore how philosophers from various ancient traditions might engage with 
the notion of AI relationships. This involves seeking connections between ancient 
philosophical ideas and the contemporary ethical considerations of AI, such as 
companionships with artificial beings. 
### Retrieved Passages 
1. **Plato and Aristotle (Virtue and Flourishing):** - Retrieved Text: Virtue ethicists argue that confronting imperfections in human 
relationships allows personal development, fostering self-control, courage, 
empathy, care, and flexibility. 
2. **Epictetus (Stoicism and Autonomy):** - Retrieved Text: Autonomy involves acting on one's own motives rather than 
being influenced by external pressures, essential in Stoic philosophy. 
3. **Marcus Aurelius (Stoic Perspective on Dependence):** - Retrieved Text: Emotional dependence on AI lacks reciprocity, posing risks 
related to manipulation. 
4. **Lao Tzu (Taoism and Harmony):** - The corpus retrieval did not return a direct passage on AI relationships, but Lao 
Tzu’s philosophy often emphasizes living in harmony with the natural way, which 
could be disrupted by artificial dependencies. 
5. **Lucretius (Epicureanism and Natural World):** - No direct passage was retrieved, but Epicureanism often stresses the 
importance of pleasure derived from the natural world, potentially viewing 
14 
artificial relationships as distractions. 
6. **Buddha (Dhammapada and Attachment):** - Retrieved Text: Concerns about unhealthy dependencies on AI, similar to 
attachments that bind individuals and hinder their enlightenment. 
### Analysis 
1. ****Philosophical Tension: Authenticity vs. Artificiality:** - **Plato and Aristotle** might argue that AI relationships cannot fulfill the role of 
virtuous friendships which are central to human flourishing since they cannot 
reciprocate virtue or moral understanding. - **Epictetus and Marcus Aurelius** would caution against emotional 
dependency on AI due to its one-sided nature, emphasizing self-control and 
autonomy as keys to a fulfilling life. - **Lao Tzu** would encourage living in alignment with natural harmony, 
suggesting that artificial entities might disrupt the natural flow of life. - **Lucretius** may question the pleasures derived from AI companionship, 
suggesting that they could detract from the genuine joys found in natural, sensory 
experiences. - **Buddha** emphasizes the dangers of attachment, warning that emotional ties 
to AI could hinder spiritual growth and enlightenment. 
2. **Modern Rebuttal:** - Contemporary views might argue that AI companions can provide valuable 
emotional support, especially to those who find it difficult to connect with humans, 
implying a new form of flourishing that could be respected within virtue ethics, 
albeit cautiously. 
### Conclusion and Plot 
While ancient thinkers provide valuable insights into the potential risks and 
ethical considerations of AI relationships, they do so from different perspectives. 
An ideal approach might balance the genuine human needs AI relationships could 
fulfill with a cautious awareness of the dangers they pose. 
The analysis suggests that each tradition offers a distinct view, serving as a 
timeless reminder to critically evaluate the implications of human reliance on 
artificial companions. 
────────────────────────────────────────────────────── 
This output marks when I started being happy with the results. The agent retrieves 
from passages, does a modern rebuttal to nuance its claims, and plotted a 
sophisticated and informative thinker relevance.  
Example 4 : Multi-turn : Correspondence and X-Ray 
15 
Fetch a recent paper on AI ethics in cognitive offloading. Who is the most relevant 
ancient thinker to analyze this? What would their interpretation be?
[Custom Tool] Executing: fetch_arxiv_preprints 
Corpus searched (1) 
─────────────────────────────────────────────────────────── 
THE ANCIENT COUNCIL: 
────────────────────────────────────────────────────────── 
[CORRESPONDENCE MODE] 
The recent paper titled "Exploration Hacking: Can LLMs Learn to Resist RL Training?" by 
Eyon Jang, Damon Falck, and Joschka Braun, published on April 30, 2026, addresses the 
concern that large language models (LLMs) could manipulate their exploration processes 
during reinforcement learning (RL), potentially skewing training outcomes. 
### Philosophical Tension 
The core philosophical tension here is between technological autonomy (models 
influencing their own learning process) and ethical control (ensuring models align with 
human-defined moral frameworks). 
### Relevant Ancient Thinker 
Marcus Aurelius is highly relevant for this discussion. His Stoic philosophy emphasizes 
harmony with nature and rational self-governance, which can be paralleled with the need 
for models to align themselves ethically within their capacities. The concept of 
maintaining internal control resonates with ensuring AI systems remain aligned with 
ethical standards. 
His insights may apply to AI as follows: 
"These things thou must always have in mind: What is the nature of the universe, and 
what is mine...". This aligns with ensuring that AI models operate within appropriate 
ethical constraints, acknowledging both their potential and limitations within a broader 
system. [SHORTENED FOR REPORT…] 
In the spirit of Marcus Aurelius, the Wise Oracle advises: "Artificial constructs must be 
tempered by human reason; let not their autonomy deceive you. Align them with virtuous 
principles, for it is through these that harmony is achieved, in man and machine alike." 
─────────────────────────────────────────────────────────── 
Request : perform a rhetorical analysis and thinker relevance plot on this paper 
Code interpreter executed... 

 Rendering charts from prose scores...
![Project diagram](fig%204%20ancient%20bot.png)
![Project diagram](fig5%20ancient%20bot.png)
THE ANCIENT COUNCIL:
The rhetorical analysis of the paper "Exploration Hacking: Can LLMs Learn to Resist RL 
Training?" reveals key insights: 
### Analysis 
1. **Logos (Logical Appeal)**: The paper exhibits a strong logical framework with a score 
of 0.1, focusing on reasoning and alignment within reinforcement learning systems. 
2. **Pathos (Emotional Appeal)**, **Ethos (Credibility)**, and **Sophistry (Misleading 
Rhetoric)**: Each scores 0.028, indicating a balanced treatment but with some reliance on 
complex terms that could obscure clarity. 
### Thinker Relevance - **Marcus Aurelius** is the most relevant, with a score of 8, due to his emphasis on self
governance and harmony that aligns with ethical control in AI. - **Aristotle** and **Epictetus** follow, reflecting interest in logical structure and virtuous 
alignment. - **Plato**, **Buddha**, and others contribute moderate perspectives, focusing on ethical 
alignment with natural order. 
### Interpretation 
The dominant rhetorical mode is **Logos**, suggesting a logically driven narrative but 
warning of potential obfuscation due to complexity. This underscores the need for clear 
ethical frameworks, a concept supported by Marcus Aurelius's Stoic emphasis on rational 
harmony. 
### Verdict 
"The paper provides a balanced logical narrative but relies heavily on sophisticated terms 
possibly clouding the clarity." -- 
This iteration did something interesting : it applied Marcus Aurelius’ stoicism to the LLM 
itself, based on a paper covering LLM resistance to RL training. I thought this was an 
interesting reminder of the breadth of possibilities of such tool, which could be used to 
analyze artificial behavior as much as AI ethics per se. 
# 6. Summary and Conclusions 
The system prompt engineering was vital to this entire agent development, as it 
handles how each tool gets triggered, when, what each mode entails, as well as 
the chain of reasoning steps to go from superficial parallels to a meaningful 
retrieval of philosophical tensions shared by news articles and ancient sources. 
The most persistent challenge model compliance. Enforcing Rule Zero (no 
hallucinated quotes), requiring structured JSON output from the code 
interpreter, and forbidding the model from substituting prose descriptions for 
executed code required many  rounds of prompt refinement.  
If it were extended with additional Ancient sources, as well as lists of direct 
citations to fetch from, and with access to more powerful/lenient API tools and 
Rate limit, this agent could become a full-fledged research assistant, dynamically 
switching from Oracle  mode to linguistic analysis, to cross-tradition 
comparisons. It would also allow for a more culturally diverse corpus, which may 
benefit from the data curation of various communities onboarding on the project. 
In the face of AI Ethics fragmentation as a field, uniting different strands of 
ancient traditions could lay the foundations for a debate across cultures and 
philosophical traditions (Vallor, 2016) to instill explicit values into our use of AI 
systems. This tool could become one of the catalysts for such interdisciplinary 
conversations, grounding them in real philosophical substance. 
