# Evaluating a Toxicity Model on Big Brother Naija Tweets

A RoBERTa model fine-tuned on the Unhealthy Comment Corpus (UCC) to flag seven attributes of unhealthy conversation, then applied to about 42,000 cleaned tweets about Big Brother Naija (BBNaija). The main question: does a model trained on news-style comments hold up on a very different community, and where does it fail?

This is a personal portfolio project. It has not been published or independently validated.

![Agreement between my hand labels and the model's top-scoring tweets](toxicity_agreement_chart.png)

## Summary

- **Model:** RoBERTa, fine-tuned on UCC for seven attributes: antagonize, condescending, dismissive, generalisation, generalisation_unfair, hostile and sarcastic.
- **Test-set performance (UCC):** macro ROC AUC 0.81, macro F1 about 0.24. The model was not tuned further because GPU credits ran out.
- **Application:** about 42,000 cleaned BBNaija tweets scored with the model.
- **Manual review:** I read and labelled 104 tweets (50 random, 54 highest-scoring across the attributes) without seeing the model's scores, then compared my labels with its predictions at a 0.5 threshold.

## Key findings

The model was reliable on overt aggression and unreliable on subtler, context-dependent attributes.

| Attribute | Top-scoring tweets I agreed with |
|---|---|
| Hostile | 6 of 6 |
| Antagonize | 9 of 10 |
| Generalisation | 9 of 10 |
| Condescending | 1 of 9 |
| Dismissive | 0 of 7 |
| Sarcastic | 0 of 10 |

In the random sample of 50, the model flagged sarcasm in 15 tweets and I agreed on 1. It flagged condescending in 7 and dismissive in 4, and I labelled none of those. A further 15 of the 104 tweets were adverts or news headlines, not conversation.

Two takeaways:

1. A single aggregate F1 score hid a clear split between attributes the model handles and attributes it does not.
2. A model trained on news comments does not transfer cleanly to fan chatter. Some hostile-sounding tweets were everyday banter, so my labels record tone, not whether something was acceptable.

## Method

1. Fine-tune RoBERTa on UCC for the seven attributes.
2. Clean the BBNaija tweets and score them with the model, giving a probability for each attribute.
3. Build a review sample of 104 tweets: 50 chosen at random and 54 taken from the highest-scoring tweets for each attribute.
4. Label each tweet by hand, working without the model's scores visible.
5. Compare my labels with the model's predictions per attribute, treating a probability of 0.5 or above as a positive.

Keep the two parts of the sample separate when reading results. The random 50 shows how the model behaves on typical tweets. The top-scoring tweets show whether its most confident calls are right. Pooling them would give a misleading error rate.

## Review file

`bbn_manual_review_v2.csv` contains one row per reviewed tweet.

| Column | Meaning |
|---|---|
| `stratum` | `random` or `top_<attribute>` (how the tweet was selected) |
| `clean` | The cleaned tweet text |
| `p_<attribute>` | Model probability for each of the seven attributes |
| `my_label_unhealthy` | My label: the attribute names that apply, separated by a comma or semicolon, or `healthy` if none; `(ad)` or `(news)` marks non-conversational posts |
| `notes` | Free-text reasoning |

Where a row carries both `healthy` and an attribute, it means the tweet was acceptable in context but its tone matched that attribute.

## Labelling definitions

These are my working definitions, written to keep my labels consistent. They are not the official UCC annotation guidelines.

| Label | Meaning |
|---|---|
| `hostile` | Angry, aggressive or threatening in tone |
| `antagonize` | Deliberately provoking or insulting to get a reaction |
| `condescending` | Talks down to someone as less intelligent or informed |
| `dismissive` | Waves away a person or view without engaging with it |
| `sarcastic` | Says the opposite of what it means, or mocks through irony |
| `generalisation` | Makes a sweeping claim about a whole group |
| `generalisation_unfair` | A generalisation that is negative, stereotyping or unjustified |

## Limitations

- One annotator, so there is no inter-annotator agreement.
- Small sample: 104 tweets, with only 6 to 10 per top-scoring group.
- A single 0.5 decision threshold; other thresholds may change the picture.
- My labels capture tone in a community whose norms I read for myself, so a different reader may disagree.
- I have not yet tested whether emojis, which are probably rare in the training data, affect the model's scores.
- The review does not retrain or fine-tune the model, and the findings are directional, not a benchmark.

## Next steps

- Add a second annotator and report agreement.
- Test whether emojis change the scores by stripping them and rescoring.
- Try per-attribute thresholds.

## Reference

Price, I. et al. (2020). *Six Attributes of Unhealthy Conversations.* Workshop on Online Abuse and Harms (WOAH). Please check the citation against the paper before relying on it.

## Repository contents

[Add your actual file list here, for example the training notebook, the scoring notebook, the analysis notebook and the review CSV.]

## How to run

[Add your Python version, the install command for your requirements file, and the order in which to run the notebooks.]

## Data

[Add where the UCC data comes from and how the BBNaija tweets were collected. Check that sharing the tweet text in this repository is allowed by the platform's terms before publishing the review CSV.]

## Author

Eric James
