*This post describes our recent work on Deep Reinforcement Learning on 
the benchmark Atari. We noticed that training and evaluation
procedures on Atari could be different from paper to paper and 
thus leading to bias in comparison. Moreover this was leading 
to difficulties to reproduce results of published works as some
training or evaluation parameter are barely explained or sometimes
not mentioned. To allow more reproducible and comparable DRL,
we introduce SABER: a *S*tandardized *A*tari **BE**nchmark for 
general **R**einforcement learning algorithms. Furthurmore, we
introduce a human world record baseline and argue that previous 
claims of superhuman performance of DRL might not be accurate. 
Finally, we propose a new state-of-the-art algorithm R-IQN by 
combining the current state-of-the-art Rainbow along with Implicit 
Quantile Networks (IQN). Last but not least, we released an open-source 
implementation of distributed R-IQN following the ideas from the 
paper Ape-X!* 

DQN's human baseline vs human world record on Atari Games 
------------ 

A common way to evaluate AI for games is to
let agents compete against the best humans. Recent examples for DRL
include the victory of AlphaGo versus Lee Sedol for Go, OpenAI Five
on Dota 2 or AlphaStar versus Mana for StarCraft 2.That's why one of 
the most used metric for evaluating RL agents on Atari is to 
compare them to the human baseline introduced in DQN. <br/><br/>
Previous works use the normalized human
score, i.e. 0% is the score of a random player
and 100% is the score of the human baseline,
which allows to summarize the performance on
the whole Atari set in one number, instead of individually 
comparing raw scores for each of the
61 games. However we argue that this human
baseline is far from being representative of the
best human player, which means that using it to
claim superhuman performance is misleading.<br/><br/>
The current world records are available online
for 58 of the 61 evaluated Atari game. On VideoPinball for exemple, the
world record is 50 000 times higher than the human baseline of DQN!
Evaluating these world records scores using the usual
human normalized score has a median of 4 400% and a mean of 
99 300% (see Figure below for details on each game),
to be compared to 200% and 800% of the current state-of-the-art 
Rainbow!<br/>
<p align="center">
<img src="file:///home/predator2/Pictures/DQN_baseline_vs_world_record.png" 
alt="drawing" width="500" />
</p>

We think that evaluating the algorithms under the world record baseline 
instead of the DQN human baseline will give a better view of the gap 
remaining between best human players and DRL agents! <br/>
And the results are clear,
Rainbow reachs only a median human-normalized score of 3% (see 
Figure 2 below) meaning that for 
half of Atari games, the agent doenst even reach 3% of the way from 
random to best human run ever! <br/><br/>
We made a [video](www.todovideo.com) with
agents previously  claimed as above human-level but far 
from the world record.
When you actually look them playing, 
they fail badly to understand the game, sometimes they just don't explore
other levels than the initial one, sometimes they are stuck in a loop
giving small amount of reward etc... A really nice example of this is the game
*Riverraid*. On this game, the agent must shoot everything and take fuel
to survive: the agent die if there is a collision with an enemy or if out
of fuel). But as shooting fuel actually gives point, the agent doesn't
understand that he could play way longer and win way more point by 
actually taking this fuel bonus and not shooting them!

SABER: a *S*tandardized *A*tari **BE**nchmark for 
general **R**einforcement learning algorithms
------------ 

In [our paper](https://arxiv.org/pdf/1908.04683.pdf) we extended the 
recommendation introduced in [Revisiting the ALE](https://arxiv.org/abs/1709.06009).
The authors of this work already pointed out some divergences in training
and evaluating agents on Atari. They proposed a set of recommendations 
for more reproducible and comparable RL including sticky actions,
 ignoring life signal and using full action set.<br/><br/>
One major parameter is left out 
of this work: the maximum number of frames allowed per episode. 
This parameter ends the episode after a fixed number of time steps even 
if the game is not over. In most of recent works, this is set 
to 30 min of game play (108k frames) and only to 5 min in some others 
(18k frames). This means that the reported scores can not be compared
fairly. For example, in easy games (e.g. Atlantis), the agent never dies
and the score is more or less linear to the allowed time: the reported 
score will be 6 times higher if capped at 30 minutes instead
of 5 minutes.<br/><br/>
We argue that the time cap can make the performance comparison non 
significant. On many games (e.g. Atlantis, Video Pinball, Enduro) the 
scores reported of Ape-X, Rainbow and IQN are almost exactly the same. 
This is because all agents reach the time limit and get the maximum
score possible in 30 minutes: the difference in scores is due to minor 
variations, not algorithmic difference. As a consequence, the more 
successful agents are, the more games are incomparable
because they reach the maximum possible score in the time cap.<br/><br/>
This parameter can also be a source of ambiguity and error. The best 
score on Atlantis (2,311,815) is reported by Proximal Policy 
Optimization by Schulman et al. but this score is almost certainly
wrong: it seems impossible to reach it in only 30 minutes! The first 
distributional paper by Bellemare et al. also did this mistake 
and reported wrong results before adding an erratum in a later version
on ArXiv.<br/><br/>
Moreover, the human high scores for Atari
games have been achieved in several hours of play, and would have been 
unreachable if limited to 30 minutes thus making impossible agents to
reach *superhuman* performance. That's why we advocate to use infinite
length time episode and only terminate when game is over or agent is 
stuck. This leads to the SABER benchmark, a set of training and 
evaluation procedures allowing for fair comparison and for reproducibility,
which parameters are sum up behind. <br/>
<p align="center">
<img src="file:///home/predator2/Pictures/SABER_parameters.png" alt="drawing" width="500"/>
</p>

Rainbow-IQN: Comparison with Rainbow on SABER
------------ 

We first re-implemented the current state-of-the-art Rainbow and evaluated 
it on SABER. We discovered the same algorithm with different evaluation
procedures can lead to really different results. This showed again the 
necessity of a common and standardized benchmark, more details can be found
in our [paper](https://arxiv.org/abs/1908.04683).<br/>
Then we implemented a new algorithm, R-IQN, by replacing the C51 
algorithm (which is one of the 6 components 
of Rainbow) by Implicit Quantile Network (IQN).<br/><br/>
As showed in the graph below, R-IQN raise better performance than Rainbow
and thus become the new state-of-the-art on Atari. However, we 
didn't have enough computational power to run multiple seeds and more
runs are needed to make a more confident SOTA claim.

<p align="center">
<img src="file:///home/predator2/Pictures/Rainbow_vs_RIQN.png" alt="drawing" width="800"/>
</p>

Open-source implementation of distributed Rainbow-IQN: R-IQN Ape-X
------------ 

We released [our code](https://github.com/valeoai/rainbow-iqn-apex) of a
distributed version of Rainbow-IQN following
ideas from the paper Ape-X: Distributed Prioritized Experience Replay.<br/>
The distributed part is really our main practical advantage over some existing RL
repositories (particularly [Dopamine](https://github.com/google/dopamine)
a really interesting open-source repository of DQN, C51, IQN and a 
*small-Rainbow* but in which all algorithms are single worker). <br/>
Indeed, if you want to use DRL algorithms for other tasks (other than 
Atari and MuJoCO), you will
quickly be limited by the *speed* of your environment. DRL 
algorithms often need a huge amount of data before reaching reasonable 
performance. This amount may be practically impossible to reach if the
environment is real-time and you cannot collect data from multiple 
environments at the same time!<br/>
Following the ideas from the paper Ape-X, we use [REDIS](https://redis.io/)
as a side servor
to store our replay memory. Multiple actors will act in their own instances
of the environment to fill as fast as they can the replay memory.
Finally, the learner will sample from this replay memory (the learner is
actually totally independant of the environment) to make the backprop.
The learner will also periodically update the weight of each actors
as shown in schema below.<br/>

<p align="center">
<img src="file:///home/predator2/Pictures/Apex_image_TOREMAKE.png" 
alt="drawing" width="800"/>
</p>

This scheme allowed me to use R-IQN Ape-X for the task of autonomous 
driving using [CARLA](http://carla.org/) as environment. 
That led me to win the
[CARLA Challenge](https://carlachallenge.org/results-challenge-2019/) 
on Track 2 *Cameras Only* showing the strength of R-IQN Ape-X as a 
general algorithm.
<br/>

