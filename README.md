# Patient-Zero
SI, SIR, SEIR models exploration

An outbreak spreads through a contact network. Later we can see who is infected, but not who started it. Can we work backwards to the source?

Short answer. Replaying the outbreak by simulation (approximate Bayesian computation, ABC) finds the source far better than "find the middle" shortcuts, and is not fooled by hubs. Once people recover, current cases alone are not enough; knowing who has recovered is what keeps the source findable.

Setup
Networks of about 85 nodes: random (ER), scale-free with hubs (BA), and small-world (WS).
Infection spreads along links with probability β = 0.3 per step. We look once 30% of the network has been infected.
Three ways to guess the source:
Jordan centre: the node closest to every infected node, i.e. the middle of the outbreak.
Rumor centrality: the node from which the outbreak could have unfolded in the most orders.
ABC: start simulated outbreaks from each candidate and keep the ones that look like the real outbreak.
Rank = the true source's position in the guessed list (1 is perfect). Random guessing ranks it around halfway.
1. SI: nobody recovers

Infected people stay infected, so the outbreak is a full record of what happened.

network	random guess	best method
ER	16	ABC, rank 2
WS	15	ABC, rank 3 (guess under 1 hop from the source on average)
BA	16	ABC, rank 5

Hubs fool the shortcuts. On the scale-free network, we placed the source at a quiet node next to a big hub:

method	rank (normal source)	rank (next to a hub)	guesses the hub
Jordan	12	16	~23%
Rumor	11	20, worse than random	~44%
ABC	5	5	~1%

Once the hub is infected, the outbreak looks centred on it, so methods that look for the middle pick the hub. ABC knows that outbreaks starting at a hub look different, so it rejects it. ABC still works when its infection rate is wrong by a factor of 2 (rank 3–4).

2. SIR: people recover

Now infected people recover and stop spreading. The source was infected first, so it is usually recovered by the time anyone looks: with a fast recovery rate (γ = 0.3), it was still sick in only 5–10% of outbreaks.

what we can see (γ = 0.3)	ABC rank, BA	ABC rank, WS
only who is sick now (random 43)	31	22.5
who is sick and who has recovered (random ~14)	4	5

Current cases alone barely beat guessing. Knowing who has recovered fixes it, because recovered people sit near the start of the outbreak and sick people sit at its edge.

3. SEIR: a hidden waiting period

Newly infected people first go through a period where they carry the infection but cannot pass it on, and tests do not catch them. With longer waiting periods, up to 45% of the outbreak was invisible and the source was almost never still sick.

With sick + recovered data, ABC still ranked the source about 4th–5th, for every waiting period tried (scale-free network, both slow and fast recovery).
The Jordan centre fell to near random as the waiting period grew.

ABC survives because people in the waiting period have not infected anyone yet. Hiding them only trims the outer edge of the outbreak and leaves its core, where the source is, intact.

Takeaways
Simulating how the outbreak actually spreads beats shortcuts that just look for the middle.
Hubs hijack those shortcuts; simulation is not fooled.
To trace an outbreak back, recovery data (who has had it) matters more than current case counts.
