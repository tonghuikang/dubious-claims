# dubious-claims
T R I G G E R E D

To complement to the fake news project, I feel that it is important to analyse the content of the text. It is meaningless to assign a score to the entire article. A low or high score, no matter how considered was the evaluation, is not helpful in giving an overview of what is wrong with the article.

We should look the claims the article made or depend on. If they are controversial or considered false, they should be flagged out. It is the duty of the article to be aware of the necessity to back up some of their claims.

In this minimum example, we want to detect sentence that claims that Obama was born in Kenya - which is clearly false.

# The minimum example

## The false claim 
Obama was born in Kenya.
https://www.snopes.com/fact-check/native-son/
If the claim is made up of only one idea (is there a better word?), then it will be that. It is conceivable to make train an algorithm today that acts as a learned English speaker who knows nothing about the current affairs - we call him a hermit - and identify all statements that claim that Obama was born in Kenya.

However, the hermit does not know the context. The birther movement gained momentum when someone claimed that Obama was not born in the Hawaii, United States but Kenya. This was used to rally for an impeachment of President Obama because the Consitution requires modern US presidents to be born in the United States.

Therefore any article that makes the following claim needs to be flagged:
Obama is not born in the United States. (ground truth paraphrased with context one)
Obama was not born in Hawaii. (ground truth paraphrased with context two)
The hermit would not be able to identify the above claims, even though they are ground truth falsehoods according to the context. 

For now, we will include the supplementary claims in the fact-checking document. Generation of supplementary claims given the main claim, the fact-checking article and Internet access seems to be difficult - I have no idea on how to go about doing it.

## Mechanics and concepts used
Word vectors. King - male + female = Queen. Similar words are close apart in the multidimensional space.
Feature vectors from images. Similar objects have similar feature vectors. We try to express an idea as a vector, and feature vectors that are close to each other are derived from a similar idea.
Parsing. The state of the art technology, minus interpretable ones since a human cannot do without context. "I saw the man on the hill with the telescope" reference(https://www.cs.upc.edu/~gatius/mai-inlp/parsing_1.pdf). However, when you know the context some interpretations do not make sense, but this seems to be a hard problem for now.

## Standards
Assuming we have the main claim and the supplementary claims, we shall see whether the following statements should be flagged.

#### Ground Truths
"Obama was born in Kenya." 
This is the ground truth, definitely should flag.

"Obama is not born in the United States." 
"Obama was not born in Hawaii."
Ground truth supplementary claims definitely should flag. 

#### Synonyms
"Obama was delivered in Kenya"
A synonym was used for "born", although it is awkward. A comparison of word vectors of subject-to-subject, verb-to-verb, object-to-object should have worked. I expect this whole technology to have already been in used to detect plagiarism.

#### Paraphrasing
"The place of birth of Obama in Kenya."
Now the object is "the place of birth of Obama", the verb is "is". Comparing object-verb-subject to another no longer works in this paraphrased falsehood. Probably what happens is these claims will be presented as a feature vector, and this feature vector is compared with each other. 

"Kenya is the place of birth of Obama"
Now "Kenya" is the object.

#### Further context required
The hermit does not know the places of the world. The hermit, based on his unexplained gifted understanding, he knows that Obama is a person, Hawaii and the United States are places (but likely not know what kind of places are they).

"Obama was born in New York."
"Obama was born in Florida, the United States."


#### Potential false positives
"Obama has visited Kenya's president's birthplace."
You cannot simply check if a statement has Obama, Kenya and birth and flag it. This sentence is ridiculous, but there are examples that it is wrong (state some?).

"Obama grew up in Kenya."
Growing up does not mean being born there, regardless whether this statement is true. However, statements like are usually found in biased articles or comments. Oh well, the hermit shouldn't be able to understand it anyway.


#### Disguised as an object
"Kenya-born Obama is trying to take our guns away."
This assumes that Obama is Kenya born. However, the hermit was tasked to identify ideas, and the ideas are a set of subject-verb-object. The idea now is compressed into an object. 

Therefore we should include convert ideas into an object and add it to the ground truth. Probably this object is considered a feature vector. 

"Traitor Kenya-born Obama is trying to take our guns away."
Would "Traitor and Kenya-born Obama"be considered one object with one feature vector? Interesting. Probably compound objectives will be separated, but how do we decide whether to separate it when there are two words in the adjective - for example "Kenya born Obama". Probably the parsing will be able to identify the nesting structure, but it is safest to do both. Still, try to think of ways to trick the system?

"Obama, who was born in Kenya, is taking our guns away."
Again, the Kenyan heritage is an adjective here. Probably the parser will identify the whole object together, and the object feature vector will be the same as the ground truth object feature variable.

"Non-native born Obama is taking our guns away."
This is a combination of using the supplementary claims and converting its idea into an object. Should we include another ground truth object? That is quite troublesome on the part of the human fact checking document writer.

#### Real examples
"Obama claims to have been born in Honolulu, Hawaii on August 4, 1961, to Stanley Ann Dunham and Barack Obama Sr. — who had married just six months prior. Some contend that this story is a complete fabrication." - from Conservapedia
Based on this quote alone, no flag. It does not directly claim that Obama is not born in Hawaii. Exploration of the veracity of the claim should not be flagged as falsehood. However, as the idea will be found in the conclusion, it will be flagged at the conclusion. We should be alerting users when such controversial claims are made without justification. Even with justification, we should still flag the idea at the conclusion since it is controversial and deserves further scrutiny, no matter how convincing is the argument.

I think I need to visit deeply conservative Facebook pages to find more of such statements.



# STAGE ONE - CLAIMS EXTRACTION

From StackOverflow 
Parse sentence into simpler sentences
https://stackoverflow.com/questions/27695995/decompose-compound-sentence-to-simple-sentences
https://stackoverflow.com/questions/26070245/clause-extraction-using-stanford-parser
https://stackoverflow.com/questions/9595983/tools-for-text-simplification-java/9606606#9606606

googled "NLP claims extraction"
https://nlp.stanford.edu/pubs/lrec_splet10-surdeanu.pdf This seemed
http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.97.5598&rep=rep1&type=pdf healthcare claims processing
seems to be monetary and legal claims rather than literal claims

googled "NLP argument extraction"
http://www.aclweb.org/anthology/W15-0510 doesn't seem to be easy to implement in Python
http://homepages.abdn.ac.uk/n.oren/pages/TAFA-15/TAFA-15_submission_18.pdf simply suggested that this field is worth exploring

Ok we know the field is known as argument mining: https://en.wikipedia.org/wiki/Argument_mining
Ongoing workshops: https://www.research.ibm.com/haifa/Workshops/argmining18/index.shtml
Seems like quite some work is done on the field already. Once again we are trying to implement to help fulfil our objective.
(Computational linguistics is actually NLP: http://aclweb.org/anthology/W/W17/)
http://aclweb.org/anthology/W/W17/W17-5115.pdf seems very relevant at first sight, but it is separating a paragraph into Claim-Premise-Anecdote-Assumption. What I want to split the statement all the claims made.

This seems to be the best bet - demo offered:
https://www.textrazor.com/demo
Seems that we can look at each "relations" (Obama - was born in - Kenya) and objects "Kenya-born Obama".


# STAGE TWO - CLAIMS SIMILARITY CHECKER
"The Microsoft Research Paraphrase Corpus (Dolan et al., 2004), which is a standard resource for the paraphrase detection task." - https://nlp.stanford.edu/courses/cs224n/2011/reports/ehhuang.pdf

Kaggle compeitions
https://www.kaggle.com/c/quora-question-pairs/discussion
https://www.kaggle.com/c/quora-question-pairs/discussion/30260

We will compare every idea expressed in the statement with the ground truth. If they match, mark the statement, show the distilled idea and the ground truth. 


# Applicability to our project.

Consider whether this will merely be a detection for opposition shrills. What value does it offer to support opposition viewpoints?

For instance, claims such as "death penalty decrease drug abuse rate" could be flagged and discussed elsewhere.

