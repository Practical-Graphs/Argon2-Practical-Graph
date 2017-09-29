# DRSample Implementation

This is an implementation of DRSample from the upcoming ACM CCS17 paper Practical Graphs for Optimal Side-Channel Resistant Memory Hard Functions by Alwen, Blocki, and Harsha. DRSample is a data-independent memory hard function (iMHF) for password hashing. The implementation is heavily based on Argon2, winner of the password hashing competition. The key difference being that the edge distribution has been modified according to DRSample. A reference to the original Argon2 specifications can be found in argon2.md in this repository. More details about the DRSample construction can be found in the CCS paper.

**Why modify the edge distribution?**
The edge distribution has been modified to mitigate attacks against Argon2. A sequence of attacks have been shown against Argon2i that show that it has lower than desired Area-Time complexity [BGS16,AB16,AB17,ABP17, BZ17]. In particular is has been shown that the amortized AT complexity of Argon2i is at best $O(n^{1.767})$[BZ17]. By contrast, DRSample has amortized AT complexity (aAT) at least $\Omega\left(\frac{n^2}{\log n}\right)$, which is essentially optimal for a data-independent memory hard function --- Alwen and Blocki show that any iMHF has aAT complexity at most $\Omega\left(\frac{n^2 \log \log n}{\log n}\right)$ [AB16]. A funtion with high aAT complexity will provide strong resistance to offline brute-force attacks on passwords.

**What does the new edge distribution look like?**
The new edge distribution selects 2 parent nodes for node i from nodes in V=[v] as follows:

1. The previous node (i-1) is selected as a parent.

2. The second parent is chosen as follows
  1. Select a random range in $g' = [1,floor( \log_2 (v)) + 1] $
  2. $g = min(v, 2^{g'})$
  3. r = random from $[max(g/2,2),g]$
  4. Select parent $i-r$

**Do the changes impact performance and throughput?**
The changes have minimal performance impacts, and our experiments show that the modifications marginally increase speed. On average, over
1000 trials, it took 0.969 seconds to run Argon2iB and 0.966 seconds to run this modified version.


**Why did you disable support for multiple lanes?**
The original Argon2 specification included a parameter specifying a level of parallelism. This has been disabled for this reference version, as concerns have been raised about the high depth-reducability when the number of lanes becomes large [AB17]

**What about data-dependent mode?**
The primary purpose of this implementaiton is to demonstrate the DRSample algorithm, however it does also support the id (independent-dependent) mode of operation. In this mode DR sample runs for half of the total time, and then the data-dependent version runs for the second half using a uniform edge distribution.
 Recent results for SCRYPT, a dMHF which uses a uniform edge distribution, have shown it to be optimally memory hard [ACPRT17]. However SCRYPT is vulnerable to space-time tradeoff attacks. For example an attacker with space $O(\sqrt{n})$ can compute SCRYPT in time $O(n^{1.5})$. We conjecture that our DRSample version of Argon2id will provide stronger resistance to space-time tradeoff attacks.

**References**
AB16 - Joel Alwen and Jeremiah Blocki: *Efficiently Computing Data-Independent Memory Hard Functions*. CRYPTO 2016.

AB17 - Joel Alwen and Jeremiah Blocki: *Towards Practical Attacks on Argon2i and Balloon Hashing*. Euro S&P 2017.

ABP17 - Joel Alwen, Jeremiah Blocki, and Krzysztof Pietrzak: *Depth-robust Graphs and their Cumulative Memory Complexity*. EUROCRYPT 2017

ACPRT17 - Joël Alwen, Binyi Chen, Krzysztof Pietrzak, Leonid Reyzin, and Stefano Tes-
saro: *SCRYPT is Optimally Memory-Hard*. EUROCRYPT 2017

BGS16 - Dan Boneh, Henry Corrigan-Gibbs, and Stuart Schechter. : *Balloon Hashing: Provably Space-Hard Hash Functions with Data-Independent Access Patterns*. 2016

BZ17 - Jeremiah Blocki and Samson Zhou: *On the Depth-Robustness and Cumulative Pebbling Cost of Argon2i*. 2017



