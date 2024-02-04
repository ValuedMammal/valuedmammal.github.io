# Blog

## Scratching your itch as a programmer

<!-- An amusing anecdote -->
It must have been 7th or 8th grade when I sat in math class on exam day patiently waiting for the teacher to go around clearing the memory from everyone's scientific calculator, and mine was no exception. Despite the fact this was a piece of technology that I *thought of* as my personal property and grew quite fond of, it was still subject to the unerring compliance demands of the scholastic authorities: *no cheating allowed*.

In those days I made sure to get my money's worth from the trusty and ever-popular [TI-83](https://en.wikipedia.org/wiki/TI-83_series). When we weren't speed-running levels of [block dude](https://azich.org/blockdude/) during class, the especially astute among us would code up and share little programs, one of which I recall was a solver of quadratic equations, and this was precisely the subject of the day's exam. When it came to light in the preceding weeks that one could simply let the calculator do the solving, word quickly spread among students anxious to make the grade.

I remember being particularly struck to see the teacher so adamantly opposed to this practice, because I was of the view that if you knew enough to write the program, you could always reproduce it within 5 minutes of having your memory cleared. On the other hand, if you trusted another kid to donate you a copy of their quadratic solver and later use it on a test, you could still end up getting poor results if the thing turned out to be bogus. That was my first glimpse into the power of software, but regrettably I didn't pursue it further at that age - my interest and talent for programming paled in comparison to what I considered the true genius that walked the halls.

Our newfound craft however wouldn't satisfy our teacher, who preferred that we "show our work" the old fashioned way when it came to test time. Where she *could have* taken the opportunity to teach some early programming concepts, instead this is one of those ironic examples where the rigidity of the curriculum can be *anti-instructive*. Fair enough, I thought; by that time I had the formula memorized anyway. I wouldn't want to be at the whim of another student to inject *his* purported implementation over auxilary cable without having verified it myself, so I practiced writing my own.

In case you've managed to supress old memories of algebra, a quadratic when plotted on a graph has the shape of a parabola, and the idea is to find the value(s) of `x` which satisfy:

> `y = ax^2 + bx + c`

and here, that dreaded formula we were taught, by setting `y = 0` and solving for `x`:

> `x = (-b +- sqrt(b^2 - 4ac)) / 2a`

The hack to automate this task was simple: prompt the user for the constants a, b, and c. Do some computation following the formula, and spit out a result if it exists. For this, I consulted the owner's manual for the TI-83. Near the back is a section on how to program the handheld machine in a language reminiscent of BASIC. The challenging part I recall was getting the grouping symbols to match in a way that obeyed the desired order of operations. Funny enough, the steps involved seemed to me little more than common sense reasoning, taking for granted the idea that molding a young mind to think logically would have profoundly lasting effects.

<!-- Scratch MIT -->
At 23 I finished college. My background was always heavy in math and science, but by this time I had still never opened a programming book. One day I stumbled upon a university lecture with David Malan of CS50 fame, and it's here I was introduced to [Scratch](https://scratch.mit.edu), which today I would say is generally recognized as a popular and effective way to ease into coding and game development for young people. In a burst of inspiration, I created an interactive greeting card in a few hours of tinkering. I still revisit it occasionally because it reminds me of a time when I was personally in need of moral support, and the resulting growth from that experience emerged from the simple act of applying creativity to a cognitive task.

<!-- Natural language -->
Another example of scratching your own itch is in natural language learning. As technology brings the world closer together, blending cultural boundaries, it's extremely common for people to be exposed to a variety of human language. As someone with a curiosity for languages, I find that in many ways learning a language helps exercise the same parts of the brain necessary to pick up a programming language, and I imagine people who have learned English out of necessity are especially poised to transfer those skills to coding. Learning a language, while at times frustrating, is very much like deciphering a code. Social scientists have a term for effectively adapting our spoken language to different environments, so called 'code switching', and the analogy is an apt one.

<!-- BTC -->
In 2017 I learned about Bitcoin and naturally absorbed everything I could at a conceptual level, still having no real knowledge or inclination to scour through the source code. Eventually it became clear that a person can only gain so much from consuming podcasts and articles - to have a real understanding involves digging into the technical details. Surprisingly, I found simply reading source code made learning the concepts much easier, and of course that applies not only to Bitcoin but to software more generally.

<!-- Today -->
When the world turned upside-down in 2020, rather than dig my heels into a political camp or succumb to collective and irrational fears, I made it a point to stop having strong opinions about news and social media and instead start building the change I want to see in the world. That shift has made a world of difference to both my productivity and wellbeing. That said, the pandemic accelerated the world in many ways. For me it cemented the notion that we can't just be passive observers of current events, but that we must be critical thinkers and agents of positive change.

I don't think it's controversial to say nearly everyone has been exposed to some aspect of programming - whether it's setting an alarm on your phone or using a TV remote to record a show. We all interact with software in some capacity, and this trend is only increasing. It doesn't hurt to know the basics, and it can only empower you to be more effective at what you're already doing. Today I get immense satisfaction from simple things like writing a script or a one-liner that I will actually use because it has some utility, even if the intention is never to show it off to the world. In this way you learn to scratch your own itch by following your intuition. The rest, as they say, is history.

Some recommendations and resources I've enjoyed:
- Courses by Dr. Chuck Severance
- Harvard CS50
- Programming Bitcoin, Jimmy Song
- The Rust Book, Klabnik and Nichols
- Rust coders Rainer Stropek, Stefan Baumgartner, Jon Gjengset
- Strager YouTube channel
- Stewart Lynch, "Sane C++"

For now I hope that gives you some more insight into the man behind the Mammal. Signing off. 🐾

<br>

## Evaluating the predictiveness of block composition under variable relay policy

Here are some early results of a study in progress that seeks to compare the accuracy of anticipated transaction data in newly confirmed blocks under different policy conditions. The available data thus far represents a trial run of the experimental methods. Plans are underway to expand the scope of analysis in the coming months.

### Question:
Will a bitcoin node configured with *mempoolfullrbf* see more of its pool of unconfirmed transactions be included in the next block than a node running the default configuration?

<!-- data -->
<!-- <img src="doc/mempool-util/examples/data.png" width="800" height="600"> -->
![](doc/mempool-util/examples/data.png?raw=true)

At first glance, I don't see much difference in the two samples, though I'd say it's far from conclusive for reasons I'll mention shortly, but overall I'm pleased to have implemented the chosen methodology. 

<!-- methods primer -->
In each of the two test cases, we captured the node's view of the mempool using bitcoind's *getblocktemplate* RPC, and this was repeated at 5 minute intervals. The result of *getblocktemplate* is, as the name suggests, a template for a consensus-valid block containing a set of transactions the node considers candidates for block inclusion. An interval of 5 minutes was chosen because it's less than the average block time of 10 minutes, so it's relatively certain we would create a new template at least once per block.

When a new block arrives, we compare the transaction data in the newly confirmed block to those present in the most recent block template. We then quantify the degree of similarity using a metric dubbed the *P2P Index Score* following a simple formula:
    
> *score* = (*actual* - *unseen*) / *actual*

where *unseen* is the number of transactions occurring in the *actual* block and not in the projected block. Hence, *score* is a number between 0 and 1, with 1 representing perfect similitude, expressed as a percent as shown in the accompanying figures. The set of scores were filtered to exclude obviously invalid ones. For example, if the duration between two consecutive blocks is less than the configured block template interval, the last block template will have been rendered stale upon scoring the first block, and hence the computed score for the second block should be discarded. That's the gist as far as methodology.

<!-- informal discussion -->
Without commenting much on the data, I would direct your attention briefly to the top figure plotting *score* vs *block time*. We see the data is noisy below a time of ten minutes, but we see a positive correlation emerge as block times grow longer than the average. While unrelated to mempool policy, this serves to build an intuition that a longer block time affords unconfirmed transactions ample time to propagate and thus a greater chance they'll be represented in the projected block. Similarly for the scatterplots of *block score*, the relative density of data above the 50% line is reassurance that the node has a somewhat accurate view of the space of unconfirmed transactions, and that there may yet be insights to glean.

There were some flaws (or at least nuances) in the execution that deserve to be mentioned:

1) **The two datasets weren't run side-by-side** as is evidenced by the block ranges. A more robust study should control this.

2) **The meaning of *unseen* in the formula for *score* is nuanced.** A transaction found in an actual block that wasn't previously projected may still be present in the local mempool, hence it was technically *seen* by our node - it just didn't make it into the next block. More context is needed to determine the cause of the discrepancy.

3) **No attempt was made to control the number or type of peer connections made.** I will say in the fullrbf scenario, an attempt was made to ensure we had at least one fullrbf peer, but in fairness, perhaps that should have been done for both scenarios. In general I'm not sure we *should* try to tightly control the quality of connections, as they can be handled automatically while requiring no input from the user. At the very least we should ensure the connection count doesn't drop so low as to risk being cut off from normal relay activity, and if the bip125-enforcing node allows a certain connection type (tor, i2p, etc), then the same configuration should be mirrored on the fullrbf node. An approach at the other extreme would have us make *only* manual connections to the same group of peers in each scenario. This option may be considered for future trials.

4) As noted in the methods primer, we discarded scores for a block that arrived below the refresh interval of 5 minutes, however **implementing the criteria that would uniformly filter invalid data is nuanced.** For instance, imagine the duration between two blocks is 2 minutes: since this is less than the defined template interval, we assume the result should be dropped. But what if a new template is crunched at minute 1, i.e. one minute after the arrival of the first block and one minute prior to the arrival of the next? In this case we should keep both scores. Sometimes blocks arrive only seconds apart. How small a range should be allowed before considering it "too soon" to compute a score for the second block? I'm open to ideas for improvement here. From the small amount of data available, we can already see the range of block scores spans all possible values making it difficult to spot outliers and leaving little indication that an invalid point might stand out from the rest.

To make matters worse, it turns out there is no optimal duration for the template refresh interval that minimizes invalid results. Early on, I naively envisioned block times spread over a normal bell curve, and that taking the time at say 2 standard deviations below the mean would capture the bulk of the data we're interested in. In fact the distribution of block time is anything but normal, because the standard deviation of block time **is the average block time**, i.e. 10 minutes. In statistics this pattern is characteristic of what's called an exponential distribution. In bitcoin, it comes as a consequence of pure probabilistic block times and, being the unrefined statistician, is something I was enlightened to learn.

Lastly, the next iteration of the study should include much more data, which entails letting the collection phase run (ideally uninterrupted) for an extended time. The raw data set from the trial run included around 1000 blocks in each scenario. A more robust dataset should include perhaps 10 times that much. Further, consider that 1000 blocks is only about half of one difficulty period. If the experiment were run over the course of say 6 months (even intermittently, as long as both nodes are collecting at the same times), then we'd have more opportunities to gather a variety of data across different mempool regimes.

<!-- link to csv -->
Raw data  
[fullrbf.csv](doc/mempool-util/examples/fullrbf.csv)  
[bip125.csv](doc/mempool-util/examples/bip125.csv)

<br>

## How to verify a signed message using Sparrow

1) Download [Sparrow](https://www.sparrowwallet.com/) for desktop. Open the app.
2) Go to **Tools** -> **Sign/Verify Message**
3) Fill in the address, message, and signature. Click 'verify'. The result is that the signature is either valid or it's not.

**Example**  
-----BEGIN BITCOIN SIGNED MESSAGE-----  
Send sats here!  
-----BEGIN BITCOIN SIGNATURE-----  
bc1qg35sdfjkpk9dwcxmx9t8qmnju9cl8g42cnry50  
J1MaTfrU2S7btQVXEVpikNpxe/XNK/DFzWN5USIXaSGdDryLHN9fOiJD4VGFTlpPSrkMU1n0Ln4kzKyix8bXQP4=  
-----END BITCOIN SIGNATURE-----  

**Why is this important?**  
A valid signature shows that the entity who signed the message controls the keys associated with the address, and it gives the sender some confidence they're not sending sats into a black hole. Likewise, you should be extremely skeptical of an entity who claims to control an address but is unable to produce a signature. Not your keys, not your coins.

**How does it work?**  
Bitcoin relies on public key crypto which is asymmetric, meaning keys come in pairs of private/public keys. The public key is derived from the private key. To serve as an effective signing scheme, signatures need to be difficult or impossible to forge, yet trivial to verify. To produce a signature, the signer finds a random point on the elliptic curve and computes a proof that is a function of the nonce, the message hash, the EC point, and the priv key. If the verifier can recover the same EC point using the signature and the pubkey, then the signature is deemed valid.

updated: 4Feb 2024
