# dubious-claims
Automating the process of eye-rolling.

For our fake news project, I feel that it is important to analyse the content of the text statement-by-statement. I feel that it is meaningless to assign a score to the entire article. A low or high score, no matter how considered was the evaluation, is not helpful in giving an overview of what is wrong with the article.

We should look the claims the article made or depend on. If they are controversial or considered false, they should be flagged out. It is the duty of the article to be aware of the necessity to back up some of their claims.

In this minimum example, we want to detect sentence that claims that "Obama was born in Kenya" - which is clearly false.

# The minimum example

## The false claim 
Obama was born in Kenya.
https://www.snopes.com/fact-check/native-son/

## Objective
Given any text that makes the false claim, the sentence (or maybe just the phrase) will be highlighted by the extension. When moused-over or clicked on the extension icon it displays the offending sentence, and link to fact-checking sites if it contradicts a fact or Quora questions if it the opinion is deemed controversial.

## Standards
We see if the following quoted statements should be flagged, and ponder how it can be done. We try to put together an algorithm that conforms to the standards.

#### Ground Truths
"Obama was born in Kenya." 
Should flag, this is the ground truth.

"Obama is not born in the United States." 
"Obama was not born in Hawaii."
Should flag. However, the machine does not know the context. Therefore these should be manually recorded along with the main false claim that should be flagged. 

#### Grammartical errors
"Obama is born in Kenya."
Should flag.

#### Synonyms
"Obama was delivered in Kenya."
Should flag, "delivered" is a synonym for "born" (though awkward). A comparison of word vectors of subject-to-subject, verb-to-verb, object-to-object should have worked. I expect this whole technology to have already been in used to detect plagiarism.

"Obama was not born in Honolulu."
Should flag. But we cannot expect the algorithm to know that Honolulu is a place in Hawaii where Obama was actually born unless we tell it. Either somewhere else in the text it is suggested that Obama was born in Kenya and it is flagged there instead, or the user went to search Obama and Honolulu and a decent search engine should suggest a fact-checking site in its first search results.

#### Paraphrasing
"The place of birth of Obama in Kenya."
Should flag. Now the object is "the place of birth of Obama", the verb is "is". An algorithm that compares object-verb-subject to another no longer works in this paraphrased falsehood.

"Kenya is the place of birth of Obama"
Should flag. Now "Kenya" is the object.

#### Erroneous
The algorithm does not know the places in the world. The algorithm knows that Obama is a person, Hawaii and the United States are places (but likely not know what kind of places are they).

"Obama was born in New York."
"Obama was born in Florida, the United States."
"Obama was born in South Africa."
"Obama was born in Africa."
Undecided. The birther conspiracy suggests that Obama was not born in the United States, so the first two should not be flagged. However, the "evidence" points to Obama being born in an African country, and any suggestion of that should be flagged. 

#### Potential false positives
"Obama has visited Kenya's president's birthplace."
Should not flag. You cannot simply check if a statement has Obama, Kenya and birth and flag it. This sentence is ridiculous and suggesting. However, and I think we should still not fault the algorithm for flagging it. 

"Obama grew up in Kenya."
Should not flag. Growing up does not mean being born there, regardless whether this statement is true. However, statements like this are usually found in biased articles or comments. 
For these two statements, we should find an example when if the algorithm is too sensitive and overshadows the real controversy of the article or causes fatigue on the user. Probably this can be done by adjusting the threshold value before the flag triggers, but if this has to be customised for every dubious claim it may not be feasible.

#### Disguised as an object
"Kenya-born Obama is trying to take our guns away."
Should flag. The object with adjective "Kenya-born Obama" assumes that Obama was born in Kenya. However, the algorithm was tasked to identify falsehoods, and for now, the falsehoods are a set of subject-verb-object. The idea now is compressed into an object. Therefore we should include convert falsehood into an object and add it to the ground truth. Probably this object is considered a feature vector. 

"Traitor Kenya-born Obama is trying to take our guns away."
"Kenya born Obama is trying to take our guns away."
Should flag. Would the algorithm consider that "Traitor and Kenya-born Obama" be considered one object that is different from "Kenya-born Obama"? Interesting. Probably compound objectives will be separated, and the algorithm detects that there is a "Kenya-born Obama" in the text. However, the algorithm needs to parse the text properly, as it should still flag "Kenya born Obama". What other ways can you think of to trick the system?

"Obama, who was born in Kenya, is taking our guns away."
Should flag. Again, the Kenyan heritage is disguised as a supplementary information here. 

"Non-native born Obama is taking our guns away."
Should flag. This is a combination of using the supplementary claims and converting the falsehood into an object. Should we include another ground truth object? That is quite troublesome on the part of the human fact checker. 

#### Contrapositives, Modifiers and qualifiers
"Obama was not born in Kenya."
"Obama was born in the United States."
Should still flag. For an article that honestly explores the controversy, it is good to provide a link to the fact-checking site that debunks the falsehood. Moreover, it is unusual for an article not about the controversy to talk about Obama's birthplace. Again, we need to look out for cases where the flagging the truth is excessive and obscure the more controversial claims the article make.

"Obama was allegedly born in Kenya."
Should still flag, even though "allegedly" is often used in journalism when the claim has not been conclusively proven.

"Obama may have been born in Kenya."
"Obama may well have been born in Kenya."
Should still flag. Suggestive, but not a claim. 

"Obama is definitely born in Kenya"
Should flag. We expect the algorithm to not understand sarcasm, even if he does he will read the only the literal meaning instead.

"People who claim that Obama is born in Kenya are idiots."
"People who claim that Obama is born in Hawaii are idiots."
Should flag. Since the controversy is mentioned.

#### Two statements
"Obama claims to have been born in Honolulu, which is a complete fabrication."
Should flag. The second clause negates the claims made by the first clause.

"Obama claims to have been born in Honolulu. This story is a complete fabrication."
"Obama claims to have been born in Honolulu. Some contend that this story is a complete fabrication." - from Conservapedia, truncated. 
Should flag. It is difficult to expect an algorithm that investigates sentence by sentence to understand that the first statement is being negated in the text. Nevertheless, contrapositives should be flagged, unless it produces mostly false positives.


# Proposed method and literature review
We propose the analysis to be done in two stages.

# STEP ONE - CLAIMS EXTRACTION
Given a compound sentence, identify and separate all the ideas in the sentence.

### Dependency Parsing
This demo offered best showcased the capabilities that we want, though does not perform as we wanted in more complex sentences.
https://www.textrazor.com/demo

"Barack Hussien Obama was born in Kenya." <br>
Subject |    Predicate |    Object <br>
Barack Hussien Obama | was born in | Kenya 

"Kenya-born Obama is taking our guns away." <br>
Subject |    Predicate |    Object <br>
Kenya-born Obama | is taking away | our guns

"Obama, who is born in Africa, has violated the US constitution." <br>
Subject |    Predicate |    Object <br>
who    | is born in | Africa <br>
Obama who is born in Africa | has violated | the US constitution

"People who claim that Obama was born in Hawaii are idiots."
Subject |    Predicate |    Object <br>
who | People claim | that Obama was born in Hawaii are idiots <br>
who | claim | that Obama was born in Hawaii are idiots <br>
who Obama | claim was born in | that Hawaii are idiots <br>
Obama | was born in | that Hawaii are idiots

Even though it is under "relations", relations actually mean another thing. https://www.nltk.org/book/ch07.html Relation extraction seems to be about identifying relations like location, time, or organisation for each applicable object. "[ORG: 'McGlashan &AMP; Sarrail'] 'firm in' [LOC: 'San Mateo']". 

### Literature Review
From StackOverflow 
Parse sentence into simpler sentences
https://stackoverflow.com/questions/27695995/decompose-compound-sentence-to-simple-sentences
https://stackoverflow.com/questions/26070245/clause-extraction-using-stanford-parser
https://stackoverflow.com/questions/9595983/tools-for-text-simplification-java/9606606#9606606

googled "NLP claims extraction"
https://nlp.stanford.edu/pubs/lrec_splet10-surdeanu.pdf
http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.97.5598&rep=rep1&type=pdf healthcare claims processing
These seem to be monetary and legal claims rather than literal claims

googled "NLP argument extraction"
http://www.aclweb.org/anthology/W15-0510 doesn't seem to be easy to implement in Python
http://homepages.abdn.ac.uk/n.oren/pages/TAFA-15/TAFA-15_submission_18.pdf simply suggested that this field is worth exploring

Argument mining?: https://en.wikipedia.org/wiki/Argument_mining
Ongoing workshops: https://www.research.ibm.com/haifa/Workshops/argmining18/index.shtml
Conference proceedings: https://argmining2017.wordpress.com/program/

### Current solution
https://github.com/philipperemy/Stanford-OpenIE-Python
However, this uses Java, which can be troublesome. This is an example of it working.
https://github.com/tonghuikang/Stanford-OpenIE-Python/blob/master/test.ipynb

# STEP TWO - CLAIMS SIMILARITY CHECKER
Given an idea, compare it with a repository of ideas tagged as controversial or false.

### Literature Review
We will compare every idea expressed in the statement with the ground truth. If they match, mark the statement, show the distilled idea and the ground truth. 

"The Microsoft Research Paraphrase Corpus (Dolan et al., 2004), which is a standard resource for the paraphrase detection task." - https://nlp.stanford.edu/courses/cs224n/2011/reports/ehhuang.pdf

Kaggle competitions
https://www.kaggle.com/c/quora-question-pairs/discussion
https://www.kaggle.com/c/quora-question-pairs/discussion/30260
However, we need to know the difference. This is optimised for comparison between two questions that are suspected to be similar. This is different from our use case of comparing one sentence with a list of sentences. 

### Current solution
We will use Smooth Inverse Frequency (of Word embeddings to generate sentence embeddings). https://github.com/tonghuikang/SIF/blob/master/examples/sif_embedding.ipynb
It is claimed by the paper to be better than LSTM or RNN methods. It is a weighted sum of the word vector (generated with word embeddings), and the weighted are inversely related to how often the words appear in the text. "Smooth", in my understanding, refers to a cutoff of the maximum possible weight is smoothened. It appears to be better than Facebook's InferSent: https://github.com/tonghuikang/InferSent/blob/master/encoder/demo.ipynb. Nevertheless, we will include both numbers.

# THE DEMONSTRATION
For the demonstration, we will make a colour map. On the x-axis is the list of documented dubious claims. On the y-axis is the list of statements made in the article. 

With the two methods SIF and InferSent we will compute the semantic similarity between all combinations of the documented dubious claims and article claims. This will degree of similarity will be plotted with colours, and we will see if the algorithm is accurate and minimising false positives and negatives. This is my next step. 

# Critical Prediction of Netizens' support
The most egregious dubious claims are made by the opposition supporters. They are the loudest on the Internet, with a variety of sites such as STR, TOC, ASS. The only pro-PAP site I could think of that toe the line on cultivating commentating of inflammatory comments is FAP. The question is, what is in store for opposition supporters?

For instance, claims such as "death penalty decrease drug abuse rate" could be flagged and discussed elsewhere. Moreover, controversial but potentially non-rebuttable opinions like "Halimah is a puppet" should be flagged, but may be validated through discourse.

# In a picture
![alt text](https://i.imgur.com/s5CDpsa.png "Demo")
