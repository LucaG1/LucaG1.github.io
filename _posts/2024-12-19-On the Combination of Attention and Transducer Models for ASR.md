---
title: On the Combination of Attention and Transducer Models for ASR (Master Thesis)
date: 2024-12-19 20:00:00 +0100
categories: [university]
tags: [thesis, asr]     # TAG names should always be lowercase
description: In this post I summarize my master thesis.
math: true
---

Title: On the Combination of Attention and Transducer Models for Automatic Speech Recognition

Code repository: [https://github.com/rwth-i6/i6_experiments/tree/main/users/gaudino](https://github.com/rwth-i6/i6_experiments/tree/main/users/gaudino)

Supervised by [Mohammad Zeineldeen](https://mmz33.github.io/) and Prof. Hermann Ney

Chair of Computer Science 6: Machine Learning and Reasoning

[Group: Machine Learning and Human Language Technology](https://www-i6.informatik.rwth-aachen.de/web/Homepage/index.html)

RWTH Aachen University

## Abstract

The task of automatic speech recognition is nowadays approached using large statistical models based on deep neural networks. Two complementary directions of research are attention models and models based on weighted finite state transducers (WFST). In this thesis, we investigate different methods to combine models from the two directions in order to further reduce the word error rate of our systems. We categorize the different combination methods into three areas: multi-task learning, combination in search and joint modelling. 

In the framework of multi-task learning, the encoder neural network is shared between the attention model and a WFST-based model. Then, the individual losses are combined during training. This combination method is well known in the research community, and our experiments confirm that it stabilizes convergence and in some cases improves the final word error rate.

Regarding the combination in search, we conduct extensive experiments on the combination of attention and connectionist temporal classification. We analyze three different ways to organize the search under three different language model conditions. We combine the different branches of a jointly trained model and compare to the combination of two separately trained models. All results are provided for two research corpora and an industrial corpus. We achieve consistent improvements using joint search approaches, where the combination of two different models is slightly favoured over combining the different branches of a single model. Moreover, the results indicate synergies of joint search with the use of an external language model. 

A tighter integration of attention and WFST-based models can be the key to find promising new approaches. In this thesis, we provide a structured literature review on the topic. We introduce the concept of latent attention models and point out a set of modelling choices that are reflected in current research.

## Datasets

- Librispeech 960h [Panayotov & Chen+ 2015] 

- Tedlium 2
- Industry data provided by AppTek.ai

## System Overview

![system-overview](/media/24-12-19-MT/overview.png)
_E2E ASR pipeline. During training, the parameters of the encoder,
decoder, and external language model are learned using pairs of input
and output sequences. The learned models are then used in recognition
to find the output sequence for a given input sequence of audio data._

## Model Structure

### Comformer Encoder

![conformer](/media/24-12-19-MT/conformer.png)
_Conformer model architecture (adapted from [Gulati & Qin 2020])._

### Attention Decoder

![attention](/media/24-12-19-MT/attention.png)
_Visualization of the cross-attention decoder._

### Transducer-based Models: CTC and RNN-T

![ctc](/media/24-12-19-MT/ctc.png)
_CTC topology (adapted from [Graves et al. 06]). Edge weights are set to $$p_t(k\vert x_1^T)$$ of the target node at $$(t, k), k\in \Sigma'$$. $$p(ALEX\vert x_1^T)$$ is given by the sum over all paths._

## Multi-Task Learning
The idea of MTL is to subject the system to more than one loss during training. In case of attention and WFST-based modelling approaches, this means that the decoder structures of both approaches are added to the same encoder.


$$
\begin{align*}
    \mathcal{L}_{mtl} &= \lambda_1 \mathcal{L}_{att} + \lambda_2 \mathcal{L}_{ctc}
\end{align*}
$$

We observe more stable and faster convergence.

![mol_plot](/media/24-12-19-MT/plot_loss_mol-1.png)
_Plotted loss values on the development set of LibriSpeech 960h for the first 50 epochs of training the attention model with and without additional CTC losses. The standalone attention model requires training for six epochs before it converges, while the model using CTC auxilary losses already converges after two epochs._

Our results can be interpreted as a confirmation of the benefits of joint
training with attention and CTC reported in [Kim & Hori+ 2017].

## Combination in Search

Different ways to organize joint search of attention and CTC:

- Two pass rescoring
- One-pass label-synchronous search
- One-pass time-synchronous search

We differ between combining the different branches of a single system, in contrast to combining two separately trained systems.

Different LM conditions:

- No external LM
- External LM, but no internal language model (ILM) correction
- External LM and ILM correction

### Results

- Experiments on 120 different model constellations

__Can we confirm that joint attention and CTC search is beneficial for
recognition performance?__

For one-pass joint recognition, all our experiments show improved performance. We give a conservative estimate of 5% relative improvement in WER.

__How do different joint search approaches compare to each other?__

Both, one-pass label-synchronous and one-pass time-synchronous joint search require increase computation time and memory. 

$$
\begin{align*}
    % \mathcal{O}_{\text{two-pass}} &= \mathcal{O}_{\text{att}} \cdot S + \mathcal{O}_{\text{ctc-forward}} = \mathcal{O}_{\text{att}} \cdot S + B \cdot T \cdot S\\
    \mathcal{O}_{\text{att-dec}} &= (\mathcal{O}_{\text{att}} + \mathcal{O}_{\text{lm}}) \cdot S\\
    \mathcal{O}_{\text{label-sync}} &= (\mathcal{O}_{\text{att}} + \mathcal{O}_{\text{lm}} + \mathcal{O}_{\text{ctc-prefix-score}}) \cdot S\\
    \mathcal{O}_{\text{time-sync}} &= (\mathcal{O}_{\text{att}}+ \mathcal{O}_{\text{lm}}) \cdot T
\end{align*}
$$

__Does the combination of two separately trained systems perform better than the combination of the branches of a single, jointly trained model?__

We only have a few results that allow for a fair comparison, as the improvement depends on the quality of the combined systems. 

In case of a single system, combined search improves
0-4% relative on the test-other set of LibriSpeech 960h. The results for combining two systems show 5-7% relative improvement.

__How does joint search behave together with using an external LM and with
ILM substraction?__

When combining the two branches of a single system, the results indicate a synergy between the use of an external LM and joint recognition with CTC.

## Joint Modelling of Attention and Neural Transducers

The thesis provides an extensive literature review. The resulting models
are part of the family of latent attention models [Zeyer & Schlüter+ 2021].

## References

[Bahdanau & Cho+ 2015] D. Bahdanau, K. Cho, and Y. Bengio, “Neural machine
translation by jointly learning to align and translate,” in Int. Conf. on Learning
Representations (ICLR) 2015, San Diego, CA, USA, May 2015.

[Graves & Fern´andez+ 2006] A. Graves, S. Fern´andez, F. Gomez, and
J. Schmidhuber, “Connectionist temporal classification: Labelling unsegmented
sequence data with recurrent neural networks,” in Proceedings of the
23rd International Conference on Machine Learning 2006, New York, NY, USA,
2006, ICML ’06, p. 369–376, Association for Computing Machinery.

[Gulati & Qin+ 2020] A. Gulati, J. Qin, C. Chiu, N. Parmar, Y. Zhang, J. Yu,
W. Han, S. Wang, Z. Zhang, Y. Wu, and R. Pang, “Conformer: Convolutionaugmented
transformer for speech recognition,” in 21st Annual Conference of
the International Speech Communication Association, Interspeech 2020, Virtual
Event, Shanghai, China, October 25-29, 2020 2020, H. Meng, B. Xu, and T. F.
Zheng, Eds. 2020, pp. 5036–5040, ISCA.

[Prabhavalkar & Hori+ 2024] R. Prabhavalkar, T. Hori, T. N. Sainath, R. Schl¨uter,
and S.Watanabe, “End-to-end speech recognition: A survey,” IEEE ACM Trans.
Audio Speech Lang. Process., vol. 32, pp. 325–351, 2024.

[Sudo & Shakeel+ 2023] Y. Sudo, M. Shakeel, B. Yan, J. Shi, and S.Watanabe, “4d
ASR: joint modeling of ctc, attention, transducer, and mask-predict decoders,” in
24th Annual Conference of the International Speech Communication Association,
Interspeech 2023, Dublin, Ireland, August 20-24, 2023 2023, N. Harte, J. Carson-
Berndsen, and G. Jones, Eds. 2023, pp. 3312–3316, ISCA.

[Vaswani & Shazeer+ 2017] A. Vaswani, N. Shazeer, N. Parmar, J. Uszkoreit,
L. Jones, A. N. Gomez, L. Kaiser, and I. Polosukhin, “Attention is all you need,”
in Advances in Neural Information Processing Systems 30: Annual Conference on
Neural Information Processing Systems 2017, December 4-9, 2017, Long Beach,
CA, USA 2017, I. Guyon, U. von Luxburg, S. Bengio, H. M. Wallach, R. Fergus,
S. V. N. Vishwanathan, and R. Garnett, Eds., 2017, pp. 5998–6008.

[Watanabe & Hori+ 2017] S. Watanabe, T. Hori, S. Kim, J. R. Hershey, and
T. Hayashi, “Hybrid ctc/attention architecture for end-to-end speech recognition,”
IEEE Journal of Selected Topics in Signal Processing, vol. 11, no. 8, pp.
1240–1253, 2017.

[Zeineldeen & Zeyer+ 2024] M. Zeineldeen, A. Zeyer, R. Schl¨uter, and H. Ney,
“Chunked attention-based encoder-decoder model for streaming speech recognition,”
in IEEE International Conference on Acoustics, Speech and Signal Processing,
ICASSP 2024, Seoul, Republic of Korea, April 14-19, 2024 2024. 2024,
pp. 11331–11335, IEEE.

[Zeyer & Schlüter+ 2021] A. Zeyer, R. Schl¨uter, and H. Ney, “A study of latent
monotonic attention variants,” CoRR, vol. abs/2103.16710, 2021.
