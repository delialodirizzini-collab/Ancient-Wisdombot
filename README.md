# Ancient-Wisdombot
An agent analyzing AI ethics through an  ancient corpus, built on the OpenAI Responses API. Interprets contemporary  news and papers in Artificial Intelligence ethics through the philosophy of seven ancient thinkers, selected to represent various traditions:  Plato, Aristotle, Epictetus, Marcus Aurelius, Lao Tzu, Lucretius, and the Buddha. 

# Detailed explanation : 
The agent works on three distinct modes: Oracle, X-Ray, and Correspondence, 
where each mode addresses a different kind of analytical question the user may 
ask on AI ethics. Its personality is authoritative but not pompous, being 
occasionally Socratic (sometimes returning back a question at the user). 
Why? AI Ethics suffer from a fragmentation across fields. Philosophers lack 
technical skills and Machine Learning experts tend to lack philosophical 
knowledge. As a philosophy student who is fascinated by AI, I’d love to have a 
tool that helps me map technical and ethical realities of AI development with 
core philosophical concepts from various traditions. It’d be useful for 
interdisciplinary essays, but also professional ethicists, AI regulatory bodies and 
Developers as it helps countering vague humanistic principles or purely 
technical fixes to ethical problems. Indeed ancient philosophical traditions go to 
the root of human problems and cover a wide range of concepts (on the topics of 
power, knowledge, agency, virtue, and the good life) that resonate more than 
ever in times of great societal changes and uncertainty. 
The project tests whether LLMs get better at identifying such interdisciplinary 
bridges when grounded in a curated corpus of such texts. It also helps 
understanding when it still may fail, which is a valuable ethical finding per se. 
How? With four distinct tool types: a file search over a curated vector store 
counting 49 documents (38 ancient philosophical texts split by chapter or theme, 
plus 11 contemporary AI ethics bridging papers)1, a function calling tool to four 
different external APIs (The Guardian, NewsAPI, Semantic Scholar, ArXiv), a code 
interpreter to run computational rhetorical analysis, and a growing conversation 
history that enables multi-turn philosophical dialogue. The agent is implemented 
as a Python Jupyter notebook on Google Colab. 
# 2. Assistant Design: A Schematic View 
The Ancient Wisdom agent makes use of the Responses API's growing dialogue 
list, of tool calls (RAG, API function calls) being dispatched and returned, and a 
loop that goes on until the agent produces a final synthesis. Here is a diagram of 
the design:  

The three modes have a deliberate different sequence of tool use, to cover 
various inquiries for which the Ancient Corpus might be useful.  
First, there’s the Oracle Mode, a way of channeling ancient voices to interpret 
and analyze the  latest news or papers. It works in this order : 
Live data first (news APIs) / Scholastic paper (Semantic API (peer-reviewed), 
arXiv (preprints) -> extraction of a philosophical tension -> corpus retrieval 
using philosophical keywords, derived from the tension -> final synthesis. The 
corpus is thus never queried with news headlines, which would return nothing 
relevant (as is often the case if we ask ChatGPT to engage in interdisciplinary 
reasoning), rather it retrieves from it with the philosophical concepts extracted 
from the articles.  
The X-Ray Mode is a way to assess the rhetoric style of a news or paper and 
thus understands its context and intent in one go. It also allows us to see which 
Philosophical tradition is closest to the text. It follows the sequence : News or 
paper fetch -> code interpreter computing rhetorical scores (logos, pathos, ethos, 
sophistry) on the full article text -> corpus retrieval to interpret what the scores 
mean philosophically -> visualisation via matplotlib. The code interpreter 
analyses the article; the corpus interprets the results. 
Finally, the Correspondence Mode is used to engage in deeper cross-textual 
analysis and finding of correspondances. It uses this order: broad corpus 
retrieval across several thinkers -> fetches academic paper via Semantic Scholar 
or ArXiv → does a cross-tradition comparison being explicitly skeptical of easy 
mappings. 
I am not using web search, because my vector store provides the specialized 
grounding of my agent and, being ancient, does not need to be searched for in 
recent data.  
This design helps me achieve the versatility and rigorousness to establish 
genuine interdisciplinary bridges and integrate the tools to produce fruitful 
syntheses.2 
3. Added Value: More than Mere ChatGPT or basic LLM 
My agent is an LLM wired to a source of carefully selected ancient texts that 
provide ample ground for conceptual interpretation and discussion of parallels 
between ancient philosophy and modern AI Ethics.  
# CHALLENGES AND ADAPTATIONS : 
At first, I tried to hard-encode the steps for each mode, to ensure that the LLM would correctly 
identify the mode and make the correct use of tools. However, I found that this strategy severely 
limited the breadth of possible prompts and it overlooked a major strength with the Responses 
API : it can easily detect the prompt’s intent. Therefore, the agent uses tool_choice='auto' 
throughout, letting the LLM sequence its tool use within the constraints of the System Prompt’s 
definition of each workflow.  
The creation of the two intermediary reasoning steps “-> extract a tension in the article” and “-> 
search the vector with keywords expressing that tension” were crucial: they made the output go 
from superficial and prone to hallucination to substantial and cited. I also added a chained 
reasoning structure in my system prompt to enforce these steps.

First of all, my agent retrieves live news from The Guardian and NewsAPI, and 
papers from Semantic Scholar and ArXiv published well after ChatGPT’s training 
cutoff. Secondly, my agent has textual grounding in a curated vector store of 49 
documents, where ChatGPT would need retrieving them and approximating 
parallels, often fabricating quotes or superficially engaging with them. ChatGPT’s 
training might not even include all these texts in queried form. The ground rule 
of the system prompt explicit forbids the agent from writing any quote unless 
that passage appeared in its file search in the current session. Third, the X-ray 
mode’s code interpreter computes linguistic analysis over categories defined by 
Aristotle in Rhetoric. Fourth, my agent maintains the full history of the 
conversation as it grows. Fifth, it integrates peer-reviewed, pre-prints papers 
with various news sources for its output. 

# 3. Five Tools 

File Search (RAG). The vector store is made of 49 documents of which 38 are 
ancient philosophical texts and 11 are contemporary AI ethics bridging papers 
making use of ancient theories, providing bridging examples to the agent. I had to 
split the texts per chapter and per theme, using markdown on each to guide the 
agent’s retrieval.  I selected the texts for their coverage of ethical dilemmas (ex. 
Nichomachean Ethics, Aristotle), well-being (ex. Tao Te Ching, Lao Tsu) and 
societal structures (ex. The Republic, Plato).  
Code Interpreter. The LLM writes and executes its own Python linguistic 
analysis. In X-Ray mode, it computes word-frequency ratios for logos, pathos, 
ethos, and sophistry markers across the full article text (up to 3000 characters), 
normalizes the ratio per 100 words, produces a matplotlib bar chart, and outputs 
a structured JSON block. The code is generated by the LLM based on the article 
text, not keyword (cf System Prompt). 
Function Calling 1: The Guardian API. My primary news source : The 
Guardian's API returns up to 3000 characters of full article body text per article, 
which is sufficient for significant rhetorical scoring.  
Function Calling 2: NewsAPI (with Keys). A fallback source covering global AI 
news. 

Function Calling 3: Semantic Scholar (with Keys). This fetches peer-reviewed 
academic papers on AI ethics topics, ranking them by citation number for 
credibility. I use it in Correspondence Mode and Oracle Mode to triangulate 
ancient philosophical positions with contemporary, recent scholarship. 
Function Calling 4: ArXiv. ArXiv fetches preprint papers, including very recent 
and innovative unpublished work on AI safety, alignment, and ethics. 
Each tool has a specific role in the architecture while letting the agent free to 
decide among options. The vector store file search grounds answers in ancient 
wisdom. The code interpreter provides the computational rhetorical analysis that 
a plain ChatGPT would not access or might hallucinate. The four function calls 
address the training horizon problem by fetching the latest news available from 
different sources. 

# 4. Outside Knowledge: Curated Data Sources 
A) Ancient Philosophical Corpus (38 PDFs) 
The corpus was assembled from public domain translations from Project 
Gutenberg and MIT Internet Classics Archive. I converted certain documents into
retrievable text. After a first run with full books uploaded, I went back to split each 
major work into chapter level or in themes based on my knowledge of their 
content, indicating it in the file name and markdown in the document. This 
improved outputs dramatically. The corpus includes: - - - - - - - 
Aristotle: Nicomachean Ethics (8 sections), Rhetoric (7 sections) 
Plato: Gorgias (3 dialogues), Phaedrus (2 parts), Republic (4 parts) 
Epictetus: Enchiridion (single PDF) 
Marcus Aurelius: Meditations (3 periods) 
Lao Tzu: Tao Te Ching (single PDF) 
Lucretius: On the Nature of Things (5 sections) 
Buddha: Dhammapada (4 sections) 
B) AI Ethics Bridging Corpus (11 PDFs) 
Then, to increase the agent’s output relevance and precision, I added a Bridging 
Corpus with 11 contemporary examples of papers using ancient philosophy in AI 
Ethics. This includes : 

-
Contemporary AI ethics papers by Floridi (3 papers on algorithmic ethics 
and unified frameworks), Vallor (3 papers on virtue ethics and technology), 
Gabriel & Keeling (on value alignment), Karpouzis (AI and Aristotle in 
education), QinZhu (Confucian robot ethics). 
# 5. Worked Examples 
Example 1 — Oracle Mode: AI, Labor Rules and Ethics 
Request : Synthesize last week’s news on AI Ethics and tell me how the Ancient thinkers 
would interpret it. --- 
OUTPUTS  
THE ANCIENT COUNCIL: 
────────────────────────────────────────────────────────── 
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
Example 2 — X-RAY mode: Ethos in Surveillance news. 
Request : fetch recent news on AI surveillance vs privacy. Then perform a rhetorical 
analysis on it by running your code interpreter to compute the logos, ethos, 
sophistry, pathos etc. Who saw it coming the best? 
�
� Rendering charts from prose scores... 
11 
Figure 2 X-Ray analysis over a Guardian article with Ethos as main value 
───────────────────────────────────────────────────────────
─ 
THE ANCIENT COUNCIL: 
─────────────────────────────────────────────────────────── 
[X-RAY MODE] 
The article "AI facial recognition oversight lagging far behind technology, watchdogs 
warn" from The Guardian (2026-05-03) highlights the rapid growth of facial recognition 
technology in the UK and the lack of corresponding legislative oversight. Notably, the 
Metropolitan police have nearly doubled the number of faces scanned in London over the 
past year. 
**Rhetorical Analysis:** 
1. **Linguistic Features:**  - **Logos:** Measured by the use of technical terms related to technology and legislation. - **Pathos:** Evaluated through sentiment analysis, looking for emotional language 
concerning public fears around privacy. - **Ethos:** Determined by the presence of objective statements about oversight and 
legal frameworks. - **Sophistry:** Identified by assertions and claims lacking empirical support. 
2. **Expected Findings:** - Likely to find a higher score in Ethos due to the article's reliance on official statements 
from biometrics commissioners and watchdogs. - Pathos may appear through discussion of public fear of surveillance. 
12 - Logos could be less emphasized if there is a focus on subjective claims. 
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
─────────────────────────────────────────── 
Here the agent responded as it was supposed to and computed a sophisticated 
analysis of the linguistic features. It also avoided inventing relevance where 
there was none for other thinkers. 
Example 3 — Correspondance mode: Ancients on AI 
Girl/boyfriends 
Request :  compare what thinkers across tradition from the ancient corpus would 
say on people having AI girl/boyfriends. Plot relevance score for all 7  
Corpus searched (1) 
13 
Figure 3 Philosophical relevance per thinker on the topic of AI Girl/boyfriends 
────────────────────────────────────────────────────── 
THE ANCIENT COUNCIL: 
────────────────────────────────────────────────────── 
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
⚡ [Custom Tool] Executing: fetch_arxiv_preprints 
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
�
� Rendering charts from prose scores... 
16 
Figure 4 X ray rhetorical analysis on LLM paper 
Figure 5 Relevance score on LLM resisting hacking 
───────────────────────────────────────────────────────────
─ 
THE ANCIENT COUNCIL: 
───────────────────────────────────────────────────────────
─ 
The rhetorical analysis of the paper "Exploration Hacking: Can LLMs Learn to Resist RL 
Training?" reveals key insights: 
### Analysis 
1. **Logos (Logical Appeal)**: The paper exhibits a strong logical framework with a score 
of 0.1, focusing on reasoning and alignment within reinforcement learning systems. 
2. **Pathos (Emotional Appeal)**, **Ethos (Credibility)**, and **Sophistry (Misleading 
Rhetoric)**: Each scores 0.028, indicating a balanced treatment but with some reliance on 
complex terms that could obscure clarity. 
17 
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
