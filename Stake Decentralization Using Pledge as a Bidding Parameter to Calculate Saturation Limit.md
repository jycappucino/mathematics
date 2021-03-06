CIP: TBD\
Title: Stake Decentralization: Using Pledge as a Bidding Parameter to Determine Saturation Limit\
Author: Jay Pseudonym Cappucino \<jycappucino@gmail.com>\
Status: Draft\
Type: Standards Track\
Created: 2022-02-28\
\
&nbsp;
# **Abstract**
With the current saturation limit set at 68 M per pool and with 3119 pools, the current staking mechanism can accommodate a total stake of 212.1 B ADA (68M*3119). This amount of ADA is 543% in excess of the current circulating supply of 33B. Because of this substantial saturation limit, one can set up a few pools and offer lucrative rewards to capture a sizeable amount of stake (_e.g._, Binance APY is 17.7%). This leads to centralization and weakening of the ecosystem against Sybil attack. Here, we describe a mechanism that uses pledge as a stake-bidding parameter under a close system, _i.e._, the total saturation limit is always equal to the total ADA in circulation. The proposed mechanism ensures that no single entity can get away with excessive stake without putting up a hefty pledge and expending for computing and infrastructure. This expense, likewise, confers the system a high very resistance against Sybil attack.\
\
&nbsp;
# **Motivation**
This proposal attempts to solve the ongoing stake centralization while preserving (if not enhancing) the security guranteed by the current consensus mechanism. It solves the problem by allocating pool saturation limit in proportion to pool pledge while at the same time guaranteeing that the total saturation limit is always equal to the circulating supply regardless of the number of pools and pledge status, _i.e._, whether the ecosystem is under-pledged, fully pledged, or over-pledged. The parameters under this proposal are adjusted in a very dynamic manner using a set of well-defined equations and, therefore, allows the staking ecosystem to operate without the need for frequent network consensus. Because of this mechanism, the would-be protocol not only enhances stake decentralization, it also ensures robustness of the ecosystem against any abrupt changes in the staking circumstances without intervention from the community.\
\
&nbsp;
# **Specification**
## **Nonmathematical Description**
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;There is no barrier to entry to set up a pool, but there exists an **optimal pledge** which is used as a yardstick to determine pool saturation limit in proportion to pledge.
* Individual pool saturation limit decreases exponentially when **pool pledge < optimal pledge**.
* Individual pool saturation limit increases **only incrementally** when **pool pledge > optimal pledge**.
* The ecosystem is **under-pledged** when the **total saturation limit < circulating supply**. Under this condition, pools can over-pledge to ???borrow??? additional saturation limit from the under-pledged pools. If there is still remaining saturation limit even after some pools have over-pledged, the unclaimed saturation limit will be distributed in accordance with Equation 1. However, the distributed unclaimed limit can still be re-claimed by under-pledged pools by continuing to pledge up to optimal pledge.
* The ecosystem is in **equilibrium** when the **total saturation limit = circulating supply**. Under this condition, only the under-pledged pools can continue to pledge to take back the borrowed saturation limit allocated for them. The over-pledge pools??? excess saturation limit, likewise, will continue to decrease until all excess had been returned. This and the proposition from the previous bullet point ensures that:
  - a **close system** is maintained, _i.e._, the total saturation limit is always equal to the total ADA in circulation regardless of the pledge status of the ecosystem, _i.e._, under-pledged, fully pledged, or over-pledged.
  - no single pool has a saturation limit that dwarfs other pools??? limits, encouraging decentralization.
* Optimal pledge decreases down to a minimum as the total number of pools increases but increases exponentially as the total number of pools decreases. This mechanism addresses Sybil attack:
  - optimal pledge is expensive for MPOs.
  - expense to conduct Sybil attack increases as the total number of pools increases.\
&nbsp;
## **Mathematical Description**
### _**Calculating Optimal Pledge**_
&nbsp;
![equation1](https://drive.google.com/uc?export=view&id=1O_gmZ9LwpVV3xs2RVNqaXLh3um3zfFKs)

where **_p<sub>opt</sub>_** is the optimal pledge, **??** is the amount of ADA in circulation (currently 33B), **_k_** is the total number of pools, and **_n_** is a variable that is determined to warrant an economically viable optimal pledge (**_p<sub>opt</sub>_**). Therefore, it appears that **_n_** is a variable that the community can vote for adjustment to increase or decrease **_p<sub>opt</sub>_**. In effect, it can be used to control the total number of pools (**_k_**) so that at some value **_n_**, the ecosystem gradually settles to some value **_k_** which the community thinks is the optimal value.\
\
If we set **_n_** =15,

**scenario 1** (very small number of pools):  
* **_k_** = 500,
* **_p<sub>opt</sub>_**  = ~ 774,763 ADA per pool

**scenario 2** (current number of pools):
* **_k_** = 3119 (epoch 321)
* **_p<sub>opt</sub>_**  = 47,205 ADA per pool

**scenario 3** (very large number of pools):  
* **_k_** = infinity
* **_p<sub>opt</sub>_**  = 27,440 ADA per pool\
&nbsp;

**Conclusion**: **_p<sub>opt</sub>_** becomes cheaper as the total number of pools (**_k_**) increases, but more expensive when the total number of pools decreases.\
&nbsp;
### **_Calculating Saturation Limit (??)_**


**case 1: _p<sub>a</sub> ??? p<sub>opt</sub>_**\
![equation2](https://drive.google.com/uc?export=view&id=1vwM9i8voMM6AnNtlxIzuwu8mQj1ntgi5) 
\
\
where:\
**_p<sub>a</sub>_** = actual pool pledge\
**_p<sub>opt</sub>_** = optimal pledge\
**_??<sub>unc</sub>_**  = saturation limit left unclaimed even after some pools have over-pledged. This variable is defined in Equation 7.\
**_T<sub>a</sub>_** = pool accumulated time of operation (in days) counting from day 1 of operation (read explanation below).\
**_T<sub>tot</sub>_**= total accumulated time of operation (in days) of all pools (read explanation below).

The first term of Equation 2 allocates saturation limit based on pledge, and ensures that saturation limit increases or decreases exponentially with pledge.\
\
 We wish to have **_??<sub>unc</sub>_** = 0, but if we do, the second term of Equation 2 ensures its distribution. The distributed **_??<sub>unc</sub>_**, however, can still be re-claimed by under-pledged pools by increasing their pledges.\
 \
Distribution of **_??<sub>unc</sub>_** based, yet again, on the proportion of pledge will further disadvantage those who are under-pledged. Therefore, we use accumulated time of operation,**_T<sub>a</sub>_**, as a parameter for the distribution of **_??<sub>unc</sub>_** which, in turn, ensures that the unclaimed saturation limit is distributed into pools that had been securing the ecosystem for the longest time. This manner of distribution prevents any would-be attacker, likely someone who had just registered a pool (or pools), from getting a significant portion of **_??<sub>unc</sub>_**. This mechanism is very important when the number of pools (**_k_**) suddenly decreased to a very low number, making **_p<sub>opt</sub>_** very expensive and leading to significant under-pledging.

\
**Example 1**
* **_k_** = 3119 (epoch 321)
* **_p<sub>opt</sub>_** = 47,205 ADA (at **_n_** = 15, see calculation in the previous section)
* **_p<sub>a</sub>_** = 47,205 ADA
* **_??<sub>unc</sub>_**  = 0 (we will deal with nonzero values later)
* **_??_** = 33B (current ADA in circulation)\
  \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **_??_** = $\frac{33B}{3119}$ $e^{1 - 47,205/47,205}$ = $\frac{33B}{3119}$ = 10.6M\
\
**Conclusion**: Epoch 321 saturation limit for pools with at least 47,205 ADA in pledge is 10.6M (at **_n_** = 15). The ratio 33B/3119 guarantees that every ADA in circulation has a pool to stake to without risking oversaturation. Note that the current average stake per pool is only 7.6M, suggesting that a vast number of pools are not adequately staked. Setting the saturation limit, on average, to 10.6M would make some large pools to become oversaturated which will encourage delegators of these pools to move their stakes to small pools.\
&nbsp;

**Example 2** (same variable values as in example 1 but lower **_p<sub>a</sub>_**).
* **_k_** = 3119 (epoch 321)
* **_p<sub>opt</sub>_** = 47,205 ADA
* **_p<sub>a</sub>_** = 30,000 ADA (for example)
* **_??<sub>unc</sub>_**  = 0 (we will deal with nonzero values later)
* **_??_** = 33B (current ADA in circulation)\
  \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **_??_** = $\frac{33B}{3119}$ $e^{1 - 47,205/30000}$ = 6.0 M\
\
**Conclusion**: 
Saturation limit decreases exponentially when **_p<sub>a</sub>_**<**_p<sub>opt</sub>_**, encouraging pools to achieve **_p<sub>opt</sub>_**. In an under-pledged ecosystem, pools can over-pledge to borrow saturation limit from under-pledged pools (see next case).\
&nbsp;

**case 2: _p<sub>a</sub>_>_p<sub>opt</sub>_ (<span style="background-color: yellow; color: black">**the equation is quite complex so please bear with me**</span>)**\
\
![equation3](https://drive.google.com/uc?export=view&id=1852sEfSyWtUbJPzyJ5Nbp8P4nafsMLfx)\
where:\
![equation4](https://drive.google.com/uc?export=view&id=1JhEh0dmbdUaENq512t_ftz3HyI-EY0x6)

* n=(**_p<sub>a</sub>_**\\**_p<sub>opt</sub>_**) is an integer division (example: 4\3 = 1).
* **$\frac{(x-|x|)}{2}$** returns 0 when x is positive, otherwise it returns the value of x.
* The first term in equation 3 (<span style="color: #0000FF">**blue**</span> text) is the guaranteed saturation limit since the pool exceeded **_p<sub>opt</sub>_**.
* The second term in equation 3 (<span style="color: #FF0000">**red**</span> + <span style="color: #008000">**green**</span> text) calculates saturation limit in excess of the guaranteed limit.
* The second factor of the second term in equation 3 (<span style="color: #008000">**green**</span> text), is the penalty factor that is accounted when the ecosystem is over-pledged. This factor decreases as under-pledge pools continue to increase their pledges. The decrease in the penalty factor, in turn, lowers the excess saturation limit (<span style="color: #FF0000">**red**</span> text) of the over-pledged pools. Hence, an over-pledged pool "return" the borrowed saturation limit without further intervention.\
  &nbsp;
  
**Example 1**: **ecosystem is not over-pledged**
* **_p<sub>a</sub>_** = 110,000 ADA
* **_p<sub>opt</sub>_** = 47,205 ADA (**_n_** = 15)
* **??=1**  (Please accept this for now. Proof is provided later.)
* current circulation supply (**_??_**) = 33B ADA
* current total number of pools (**_k_**, epoch 321) = 3119\
\
Because the system is not overpledged, Equation 3 is reduced to:\
\
![sample1](https://drive.google.com/uc?export=view&id=1cJg4uaHTGhaKv4pZvL5win3sTbOJeYoo)\
\
Plugging in the values,\
\
![sample2](https://drive.google.com/uc?export=view&id=1HUxMMwBfo8hAcHH8gAQn6rCboPvR3pkW)\
\
**Conclusion**: Since **_p<sub>a</sub>_**/**_p<sub>opt</sub>_** = $\frac{110000}{47205}$ = 2.33, the pool is guaranteed a saturation limit that is twice of $\frac{33B}{3119}$. The other additional limit $\frac{33B}{3119}$ $e^{-2.028}$ comes from the 0.33 fractional excess and is penalized exponentially.\
&nbsp;

**Example 2**: **ecosystem is over-pledged**.\
Here, we will provide the derivation for the penalty factor (<span style="color: #008000">**??**</span>) and we will prove that it leads to a close system, _i.e._, the total saturation limit is always equal to the circulation supply no matter how under-pledged or over-pledged the ecosystem is. The penalty factor (<span style="color: #008000">**??**</span>) has the following characteristics:
* <span style="color: #008000">**??**</span> = 1, when the ecosystem is either under-pledged or at equilibrium. Under this condition, the excess saturation limit of over-pledged pools are unaffected. In essence, they are not yet "returning" any of the borrowed excess.
* <span style="color: #008000">**??**</span> ??? 0, when the ecosystem is over-pledged, and **_p<sub>a</sub>_** of the under-pledged pools approaches  **_p<sub>opt</sub>_**. Under this condition, the excess saturation limit of over-pledged pools also approaches zero. In essence, they are "returning" the borrowed excess.\
\
We first calculate the total saturation limit (**_??<sub>total</sub>_**) excluding:
  - <span style="background-color:yellow; color:black">saturation limit from over-pledges.</span>
  - <span style="background-color:yellow; color:black">distributed unclaimed saturation limit (**_??<sub>unc</sub>_**, see second term in Equation 1).</span>
 
\
![Equation5](https://drive.google.com/uc?export=view&id=19VQyenWAntcKh8VavMpJxscc4scqT6_O)\
\
where **_p<sub>ai</sub>_** is the actual pledge of pool _i_. Since total saturation limit (**_??<sub>total</sub>_**) can never exceed circulating supply (**_??_**), the unclaimed saturation limit (**_??<sub>unc</sub>_**) is then calculated as follows:\
\
![Equation6](https://drive.google.com/uc?export=view&id=1JcYX5u499lXnhN9Ha7WSeN49d1WcSbtz)\
\
<span style="background-color:yellow; color:black">It is important to note that for the calculation of **_??<sub>total</sub>_**, we must only use **_p<sub>a</sub>_**???**_p<sub>opt</sub>_** for all pools even for over-pledging pools</span>. Realistically, some pools will be over-pledged and their total excess saturation limit (**_??<sub>ovp</sub>_**) may be added as follows:\
\
![Equation7](https://drive.google.com/uc?export=view&id=1Yj-_PGbl1WcWttDMX1acEex0e7ZFHWDM)\
\
Solving for **_??<sub>ovp</sub>_** we have,\
\
![Equation8](https://drive.google.com/uc?export=view&id=1NnoNPLEhp8cihYNvscXdXKP8lPikuADQ)\
\
Another way of expressing the total excess saturation limit (**_??<sub>ovp</sub>_**) is by using Equation 3 and is given as follows:\
\
![Equation9](https://drive.google.com/uc?export=view&id=15FezfQXXCskq6rblQdauD1jJOEtCFSpH)\
\
where _j_ is any over-pledging pool. Plugging in Equation 9 into Equation 8, we have:\
\
![Equation10](https://drive.google.com/uc?export=view&id=15iwumKXAAhPy6t98uHZpiJk0CsltAWkd)\
\
Dividing both sides of Equation 10 by the right-hand side of Equation 9 and simplifying, we have:\
\
![Equation10](https://drive.google.com/uc?export=view&id=1lQYewlKuCotMs0QQ8I3xJcc4rfddyHk3)\
\
<span style="background-color:yellow; color:black">It is very important to note that at **_??<sub>unc</sub> ??? 0_**, its value must be zero. This is very important, otherwise a close system cannot be guaranteed.</span>\
\
Now, we will prove that Equation 11 guarantees a close system, _i.e._,

<span style="color: #008000">**??**</span> = 1, when the ecosystem is either under-pledged or at equilibrium.\
<span style="color: #008000">**??**</span> ??? 0, when the ecosystem is over-pledged, and **_p<sub>a</sub>_** of the under-pledged pools approaches **_p<sub>opt</sub>_**.\
\
**Case 1**: Ecosystem is under-pledged and over-pledging pools keep over-pledging to borrow additional saturation limit: the denominator is going to increase (<span style="color:red">red</span> arrow) while the numerator is going to increase by the same magnitude through a decrease (<span style="color: #0000FF">blue</span> arrow) in the unclaimed saturation limit (**_??<sub>unc</sub>_**) :\
  \
  ![logic1](https://drive.google.com/uc?export=view&id=1-vvc_xUd8KSY5zmkR9xXP8ebZ_re3y6h)\
  \
  **Case 2**: Ecosystem is under-pledged and under-pledging pools keep pledging to claim the remaining saturation limit: the second term in the numerator is going to increase (<span style="color:red">red</span> arrow) while the third term is going to decrease by the same magnitude (<span style="color: #0000FF">blue</span> arrow):\
  \
![logic2](https://drive.google.com/uc?export=view&id=1XK1F2zG1zyaLPn2sz8rrISKPFEI0_r8B)\
\
**Case 3**: Ecosystem is over-pledged (**_??<sub>unc</sub> = 0_**) and under-pledged pools keep pledging to take back the borrowed saturation limit: the total saturation limit of under-pledging pools increases (<span style="color:red">red</span> arrow) which causes a decrease in the penalty factor (<span style="color: #0000FF">blue</span> arrow). The decrease in the penalty factor, in turn, causes a decrease in an over-pledged pool???s excess saturation limit (see Equation 3). In essence, an over-pledged pool is simply returning the ???borrowed??? excess, and its excess saturation limit approaches zero as <span style="color: #008000">**??**</span> approaches zero.\
\
![logic2](https://drive.google.com/uc?export=view&id=182u2H0u72eDxSX04vjulPkeXWzjEdZmW)\
\
**Case 4**: Ecosystem is over-pledged (**_??<sub>unc</sub> = 0_**) and over-pledged pools keep over-pledging even when there???s no longer any saturation limit left for them to borrow: the denominator increases which causes the penalty factor to decrease and, in turn, decreases the excess saturation limit of an over-pledge pool but leave the saturation limit of the under-pledged pools unaffected. This situation will cause the total saturation limit to go below the circulation supply (**_??<sub>total</sub> < ??_**) even if the ecosystem is over-pledged. This decrease should be sufficient to prevent over-pledged pools from continuing to over-pledge when **_??<sub>unc</sub> = 0_**. However, such weakness may possiby be exploited. Hence, when **_??<sub>unc</sub> = 0_**, any pledge from an over-pledged pool should rejected.\
\
![logic2](https://drive.google.com/uc?export=view&id=1j45ZEEhqwDDJoXcVxs4p1NqixegXGbmM)\
\
&nbsp;
&nbsp;
## **_Sybil Attack_**
\
**Simplifying scenario: ecosystem average pledge = optimal pledge and 33B ADA staked.**

**scenario 1** (small number of pools):\
**_n_** = 15 and **_k_** = 500,\
**_p<sub>opt</sub>_** = ~ 774,763 ADA

A Sybil attack requires 501 nodes, number of pools (**_k_**) totals 1001 (500 + 501), and optimal pledge (**_p<sub>opt</sub>_**) decreases to 148, 011 ADA. Therefore, an attack requires 501 pools that need to be fully saturated (16.5M in stake) and a total of ~74M ADA (501*148,011) in pledge.

**scenario 2** (large number of pools):\
**_n_** = 15 and **_k_** = 3119 (epoch 321),\
**_p<sub>opt</sub>_** = 47,205 ADA

A Sybil attack requires 3120 nodes, number of pools (**_k_**) totals 6239 (3119 + 3120), and optimal pledge (**_p<sub>opt</sub>_**) decreases to 35,992 ADA. Therefore, an attack requires 3120 pools that need to be fully saturated (16.5M in stake) and a total of ~112M ADA (3120*35,992) in pledge.\
\
&nbsp;

# **Rational**
The steady centralization of stake concentrated to only a few MPOs primarily arises from the substantial saturation limit (68M) allocated for each pool. This large allocation allows moneyed MPOs to gain substantial delegation simply by setting up multiple pools and offering lucrative rewards. If left unchanged, the current state of affairs deters would-be SPOs from setting up pools as well as discourages current SPOs from continuing to operate because, for a significant number of them, the cost/reward is economically disadvantageous. This only leads to further centralization.

To mitigate the problem outlined above, we recognized that saturation limit needs to get decreased to some minimum that allow fair distribution of stake but avoids the potential risk of oversaturation. We may do this by allocating saturation limit based on pledge. This idea was first explored by Casey Gibson in his CIP which you may find <a href="https://github.com/cardano-foundation/CIPs/pull/163">here</a>. We found that there may be potential weaknesses to his ideas, but the same weaknesses are addressed in our proposal. These weaknesses are:
* **The Gibson proposal appears to be less effective at solving stake centralization.**\
To illustrate this argument, let's detemine how the Gibson proposal would affect a current MPO that has 60 pools and controls 2.8B ADA. First, we take note that this MPO has total control of the stakes delegated to it. The Gibson proposal, in its current form, allocates 100% of K (65M in stake) for every 500,000 ADA. Therefore, each pool requires 65.5M to setup and get fully saturated. The number of pools that this MPO needs to retain all 2.8B stakes is just 43 pools ($\frac{2.8B}{65.5M}$), which is lower than the MPOs current number of pools. The total pledge would amount to 21.5M, but this pledge is irrelevant for this MPO because it has total control of the stake delegated to it.\
\
The numbers from our proposal would be 282 pools and 12.8M in pledge to retain all 2.8B in stake at **_n_** = 15 and **_k_** = 3119 (epoch 321). The calculation is as follows\
\
**_p<sub>opt</sub>_** = 33B*$e^{1-15*(1+erf(-100/(3119-60+pools)))}$\
**_??_** = $\frac{33B}{3119-60+pools}$\
**_pools_** = 2.8B/(**_??_**+**_p<sub>opt</sub>_**)\
\
We have three equations and three unknowns which resolves to **_pools_** = 282 and total pledge to 12.8M (282 pools * 45.5K in pledge)\
&nbsp;
* **The Gibson proposal may disenfranchise a significant portion of ADA in circulation from getting staked at low pool number.**\
Because the Gibson proposal does not gurantee that the total saturation limit of all pools is equal to the amount of ADA in circulation, there exists a significant risk of disenfranchising some portion of ADA in circulation from getting staked. For example, if the number of pools abruptly dropped to 500, all pools need to pledge at maximum (500,000 ADA to get 68M saturation limit) so that the ecosystem can achieve a total saturation limit that is approximately equal to the total ADA in circulation. However, all pools pledging at maximum is a very unlikely scenario, leaving some ADA disenfranchised. Risk of disenfranchisemnt exists even at the current number of pools. <span style="background-color:yellow; color:black">In contrast, the total saturation limit under our proposal is always equal to ADA in circulation.</span>
&nbsp;

* **The Gibson proposal may require frequent network consensus to tweak parameters.**\
Since the Gibson proposal rely on parameters that can only be changed when there is network consensus, such design is less robust against abrupt changes in the ecosystem, _e.g._, when there is a need for the number of validators to scale up with demand or when the number of pools declines abruptly due to extreme events. <span style="background-color:yellow; color:black">Our proposal scales up and down without further intervention because the protocol is governed by equations.</span> \
\
&nbsp;
# **Copyright**
  This CIP is licensed under [CC-BY-4.0](https://creativecommons.org/licenses/by/4.0/legalcode)
  
