# **Potential projects**

## ***Explore new language models***

The encoding framework allows us to compare different models, where different stimuli features act as different hypotheses about the underlying function or representation of the neural activity. The Podcast ECoG dataset comes with some basic stimuli features (e.g., acoustic, phonetic) and word embeddings from LLMs (e.g., GPT-2 and Whisper). Newer language models have been developed and recently since then. Some are more powerful or add additional modalities. While others are more cognitively plausible. This project proposes that you learn about a new model, extract embeddings from it, and use it in an encoding analysis. Then, you can compare the new model’s performance with others to draw conclusions and insights. Here is a list of models to start with:

- Speech-based models ([SeamlessM4T](https://huggingface.co/facebook/seamless-m4t-v2-large), [AudioLM](https://arxiv.org/abs/2209.03143))  
- Compare language-only to language-vision models like [CLIP](https://openai.com/index/clip/)  
- LM embeddings of sentence/paragraph/section-level summaries (e.g., [SONAR](https://arxiv.org/abs/2308.11466))

[HuggingFace](https://huggingface.co) offers easy access to many models through the [transformers](https://huggingface.co/docs/transformers/index) library.

## ***Time-resolved regression***

ECoG recordings have a high temporal resolution (at least 512 Hz in our dataset). However, embeddings or features derived from a transcript create one representation per word. And abstract knowledge or thoughts could be argued to inhibit an even lower temporal frequency. This creates a mismatch that needs to be mitigated: what is the alignment between words and the signal? In our tutorials and publications, we selected time points from the complete ECoG for the *onset* of each word in order to match the transcript length and be able to train encoding models. Then, we shift each time point by some *lag* and train a separate encoding model. An alternative strategy would be to keep the complete ECoG recording and instead adapt the word embeddings to match it. This could entail one of several possible approaches:

1. A naive approach would be to forward fill the embeddings to fill in gaps   
2. Interpolate missing time points between embeddings  
3. Use embeddings from multiple layers  
4. Sentence-level encoding models \- use sentence encoder on the language side and train nonlinear models making no assumptions regarding signal lags (initial code already exists)  
5. High temporal resolution feature spaces \- train models on phonemes or on high temporal-resolution acoustic features (e.g using whisper in 50 Hz)

## ***Intersubject correlation analysis***

Intersubject correlation (ISC) is a simple but powerful method used to identify shared neural signals *between* subjects. It effectively decomposes a signal into the sum of 3 components: a subject’s idiosyncratic signal, the shared signal with others, and an error term ([Nastase et al., 2019](https://doi.org/10.1093/scan/nsz037)). It works like this: you take the time series of one subject and correlate it with the *average* activity of all other subjects, per brain region. Once you do this for all subjects, you’ll have a correlation for each subject, which you can then average into one value. A high correlation value indicates that this region has a high shared component across subjects, and thus is driven by the stimulus. A critical assumption, however, is that the signals you collect for all subjects come from the *same brain region*. Otherwise, you would end up with more idiosyncratic and less shared signal components. Given that ECoG electrode coverage is different for each subject, this kind of analysis is more difficult. However, many subjects often have electrodes in similar places (e.g., superior temporal gyrus and inferior frontal gyrus). Thus, ISC can be performed by grouping electrodes based on location if there are enough electrodes amongst all subjects.

## ***Linguistic information in other frequency bands***

The high temporal resolution of ECoG allows us to filter the raw signal into frequency bands up to 256 Hz. Our existing analyses only used one frequency band, named high-gamma band, which is between 70–200 Hz. However, linguistic information may be present beyond this range. One approach would be to extract different [frequency bands](https://en.wikipedia.org/wiki/Neural_oscillation#Overview) based on prior literature. Another could be to use only the raw, cleaned signal and build an encoding model to *learn* its own frequency filters. An advanced analysis could look into [cross-frequency coupling](https://en.wikipedia.org/wiki/Neural_oscillation#Cross-frequency_coupling).

## ***Tapping into the residual stream***

LLMs are deep neural networks. They’re composed of multiple layers that pass information via embeddings from one layer to another by making modifications to a *residual stream*. Early work investigating encoding performance differences across layers has found that middle-to-late layers often perform the best. While early and very late layers perform more poorly. One work separated out different representations within BERT: *embeddings* after each encoder block, and *transformations* from transformer heads ([Kumar, Sumers et al., 2024](https://doi.org/10.1038/s41467-024-49173-5)). Building on this, it would be interesting to extract additional LLM embeddings from the activations before/after the MLP projections within each layer block, and/or use the residuals they add to the stream as embeddings (as opposed to using the cumulative activations *after* the residual stream as is typically done).

## ***A method for selecting “good” electrodes***

How can we identify ECoG electrodes that are responsive to the stimulus and involved in linguistic processing? One approach is to use an encoding model with embeddings from a non-contextual model, and select electrodes that have high encoding performance for further analyses. However, this may be deemed inappropriate because of the circular use of encoding analyses. Perhaps an ISC analysis combined with other frequency bands would be effective?

## ***ECoG foundation model project***

Encoding analyses map LM representations of language to brain signals. Brain signals, however, are parts of the original raw signal, extracted in a theory driven way (averaged high-gamma power activity around word onsets). Work has been done attempting to extract information from raw brain recordings using autoregressive models on [single electrodes](https://arxiv.org/abs/2302.14367). A [recent paper](https://arxiv.org/abs/2402.10251) tried establishing a much needed benchmark for this type of task. We have started training similar models in house, attempting to harness spatial information from electrode grids. Several sub projects could be interesting in this space:

1. Apply existing brain transformers (pre-trained or fine-tuned) to data and test on encoding / decoding (BrainWave, BrainBert)  
2. Create benchmarks for autoencoder performance \- simple downstream tasks for foundation models with our data: podcast volume regression, speech detection (classify perception/production/none), parts of speech classifier  
3. Implement alternative (simpler?) architectures to the current autoencoder
