## I. Preface:

Since Daniel Larimer introduced DPoS into EOS, how to better select Block Producers has become the focus of much community debate. In this regard, BOS follows EOS voting mechanism. That is, a one-vote multi-selection (maximum 30 voters) mechanism, but due to the existence of Pareto's law, the voting tends to be concentrated in the hands of a few people, also a one-vote multi-selection mechanism allows token holders to trade votes, which can ultimately lead to the Block Producer rankings being controlled, thereby causing an imbalance in the decentralization of the system, and further leading to an increased gap between  the ranks. To this end, in the design of the bet voting system, we try to introduce an improved Borda voting mechanism to alleviate this problem, in order to find a more reasonable means to make a corresponding contribution in ensuring system security and fairness.

To this end, in the design of the BET voting system, we aim to introduce an improved Borda voting mechanism[^1] to alleviate this problem in order to find a more reasonable means to make a corresponding contribution in ensuring system security and widespread distribution; which ultimately leads to a better and more inclusive distribution of WPS funds.

## II. Proof of Stake mechanism and Voting:

There are many articles about the Proof of Stake mechanism. There is no more detailed discussion here. Only the discussion of Block Producer concentration and security is discussed. For the sake of discussion, we simplify the above.  

1. We assume that all voting rights are from voters
2. We simplify the original model as a probability discovery model

Let‘s make the following assumptions: 

* Token holder's shareholding ratio is Q, 
* Secret cheating function H
* Cheating is found after loss function F(Q), maintaining system stability. 
* The general return function is X(Q)
* The cheating gain is W

*Attention:The hypothetical function here is only used for qualitative analysis*

Ao we can simply get the strategic relationship of the stakeholder:

	Judging the relationship between W+(H-1)*F(Q) and X(Q) and determining the corresponding strategy. 

Because F is a positive correlation function of Q, X is also a positive correlation function of Q, and W is a random parameter with speculation. So it can be concluded that the cheating gain will be significantly reduced with the increase of Q, under the assumption of rational natural person. Choosing to maintain fairness is a dominant strategy. So from this perspective, the POS mechanism is a stable model.  

In fact, when we further analyze the ethical parameters, we can obtain the following relationships and conclusions between the equity structure and the stakeholders' income:

A) Absolute concentration structure of equity

	If the equity is absolutely concentrated on the network operators, the supervision will be difficult, and the moral value of the network operators will decrease or even become negative.
	Operator Responsibility(→min)=Operator Morality(→-)×{1+ Operator Asset Ownership(max)}
	Stakeholders' Income(→min)=Operator's Responsibility(→min)×Operator's Ability×Operating Conditions.

B) Relatively concentrated structure of equity

	The relative concentration of equity can encourage operators, stabilize the backbone team, and generate checks and balances.
	Operator Responsibility(→max)=Operator Ethics(→max)×{1+ Operator Asset Ownership(max)}
	Stakeholders' Income(→max)=Operator's Responsibility(→max)×Operator's Ability×Operating Conditions.

C) Equity dispersion structure

	The equity is too scattered, the supervisory motivation is insufficient, and the moral value of the network operators will decrease or even become negative.
	Operator Responsibility(→min)=Operator Ethics(→-)×{1+ Operator Asset Ownership(min)}
	Stakeholders' Income(→min)=Operator's Responsibility(→min)×Operator's Ability×Operating Conditions.

From the analysis, we can determine that (B) relative concentration of equity is the preferred equity structure (for detailed knowledge, please refer to the books and papers related to equity structure).


Therefore, we can avoid the concentration of large stakeholders taking control by adopting a method of concentration relative to staked weight, on the premise of the stability of the system. So the question remains: is there an ideal voting solution?

## Voting and election

Before discussing this issue in detail, let's try to define the problem:

	We assume that there are N people participating in the election, there are a total of K people to vote, 
	and there is an arrangement in the satisfaction of each voter in the hearts of the candidates. 
	Our aim is to develop an election plan that will eventually be "fair" for everyone. 
	The sequence of voting results.

So what is fair? Here we use the five rules of Arrow[^2]:

1. Every voter has the same influence; further, it is non-authoritarian.
2. In addition to being unreasonable, voters have no other restrictions on the ordering of their minds.
3. If all voters think A > B, the election result will also show A > B.
4. The ranking of the election results for A and B should be determined only by the relative order of A and B within the file, regardless of any third party.
5. If all voters are rationally sorting the choices and voting honestly, the results of the elections should also show a rational order.

But unfortunately there are a variety of proof methods[^3][^4][^5] to prove that such "justice" does not exist, which is also the content of the Arrow impossible theorem. Since our system will be modified based on the Borda system, here we give proof of the theorem in the Borda case:

Let a given k integers m1, m2, ..., mk, let σ(1,2,...,n) be a permutation group of (1,2,...,n), and define the following mapping:

![math01](https://raw.githubusercontent.com/boscore/Documentation/master/imgs/dops_borad_count_math01.png)  

For any set of numbers t1>t2>...>tn, use the above k permutations to make an n×k matrix:

![math02](https://raw.githubusercontent.com/boscore/Documentation/master/imgs/dops_borad_count_math02.png)  

Define n numbers in turn:

![math03](https://raw.githubusercontent.com/boscore/Documentation/master/imgs/dops_borad_count_math03.png)  

Then there are:

![math04](https://raw.githubusercontent.com/boscore/Documentation/master/imgs/dops_borad_count_math04.png) 	

We know that we can find n sets of numbers for the above equation.

![math05](https://raw.githubusercontent.com/boscore/Documentation/master/imgs/dops_borad_count_math05.png)   

Corresponding

![math06](https://raw.githubusercontent.com/boscore/Documentation/master/imgs/dops_borad_count_math06.png)   

Satisfy:

![math07](https://raw.githubusercontent.com/boscore/Documentation/master/imgs/dops_borad_count_math07.png)  

In other words, the Borda Count method is also unstable. So where is the problem? In fact, our assumptions constraints such fairness.  

In his paper, scholar Saari elaborated on the above issues. One of Saari's arguments is that articles 4 and 5 of Arrow's conditions are “offset” with each other, so they have carried out the fourth rule. The amendment is as follows:

	4'. Election results The ranking of A and B should be determined only by the relative order and distance of A and B in the file, regardless of any third party.

So under this assumption, Saari proves that:

	the Borda Count election process is "fair" on the premise of more than two candidates! 
	
For specific technical details, please refer to [Saari's related papers](https://en.wikipedia.org/wiki/Donald_G._Saari#Papers). In other words, the Borda Count election process is better in this sense. Therefore, we try to adapt the BET voting system with the DPOS system under this conclusion.

## III.BET voting system.

Next we will give a general description of the voting system:

It is assumed that the number of people participating in the election is N, K people vote.

Our voting rules are as follows:

1. For any voter, it is required to give a sequence Ln of the order in which the candidates fit or not.
2. The voter's voting weight is recorded as the number of tokens held and mortgaged by Pi.
3. Given a function F(i), the function returns a score for the different sequence numbers of the sequence. The specific function will be given below.
4. The rules for scoring any candidate are as follows:

	Vk = ∑ F(i,j)*Pi 

5. Sort all candidates who participated in the election and draw the final selection sequence.

BET will adapt the following F function:

	F(i) = (N+1-i)/N

Proof:

> Suppose that any voter holds the number of Tokens in his hand, that is, the number of votes, and only one vote at a time.   <br>
> We limit each voter, assuming that any voter can vote for the number of votes held in their hands, that is, Pi, and only one vote at a time, so we can treat **each voter as a collection of voters** with the same willingness to vote, each with only one vote, further seeing this becomes a model for the Borda count to vote for the Borda count. <br>
> So it has the same theoretical characteristics as the Borda count system.

Further, for any ticket holder Pi, since he needs to vote (sort) all candidates, that is, he contributes to any candidate, but the degree of contribution is according to function F. To do this, that is to say, set Pi>Pj and F(k)<F(l) for any k>l, then there are:


	Pi/Pj > (∑F(i) * Pi)/(∑F(j) * Pj)


Among them, i, j are ranked in the disadvantage position of the other party's sequence, so the comparison of one vote and more investment scheme obviously weakens the role of the ticket.

The following is a comparison of the control function with one vote and multiple casts under different F functions.

> The area below the line is the maximum vote for a voter voter

![math08](https://raw.githubusercontent.com/boscore/Documentation/master/imgs/dops_borad_count_math08.png)  

> The area below the line is the maximum vote when F is the Borda count system.

![math09](https://raw.githubusercontent.com/boscore/Documentation/master/imgs/dops_borad_count_math09.png) 

> The maximum area below the line is the voting maximum when F is X^(-σ)

![math10](https://raw.githubusercontent.com/boscore/Documentation/master/imgs/dops_borad_count_math10.png)  

Although not rigorous, you can intuitively feel the difference between them.

Next, we will illustrate the features and advantages of the program through a few simple examples:

### Example One
Suppose there are three voters A, B, and C. For the sake of discussion, let's assume that the candidates are the same three, and assume that they vote for themselves. Our voting principle is to choose two of the three to win.

We assume that A, B, and C hold the number of tokens a, b, and c, respectively. If we assume conspiracy in a and b, if EOS's current voting system is adopted, C will lose the final right as long as `a+b>c`. But under the BET system, you need `a+2b>2c` to get the corresponding result. In the case where the differences among the candidates are not different, a and c can be elected.

### Example Two
Consider another example of bribery. Let's assume that there is a large account A with 20M tokens. The support of other candidates is only between 1M and 6M. If the EOS strategy is adopted, then the other candidates cannot reach consesus. Under the premise, A can control the entire order of the elected person. Due to the strong control of A, many candidates will choose to trade with A, which will eventually lead to the further strengthening of A's control.

But for the BET system, the situation will be different. For example, we use `(n+1-i)/n` as the F function, and n is assumed to be 25, then for the big user A, it is for the ith support. The contribution value that the account can provide is `(n+1-i)/n*2*10^7`. For example, when i is 20, the contribution value is 4.8 million; that is, when there is a Token held by a voter. When this value is exceeded, then choosing to work with this Token holder is a better strategy than working with A.

Therefore, this can weaken the control power of A to a certain extent and at the same time ensure the interests of many stakeholders. At the same time, because the plan is aim to a voter must **fill for all 25 slots in order to cast a vote**, the voter is maximizing the profit for the benefit of the rational person. In addition to yourself, the remaining votes will be voted for the most stable candidates for the system, thus ensuring the security and stability of the system.

## IV. Summary

The design is a further exploration of the existing EOS voting system, with the research results of Saari et al., which can make the BET voting system more perfect, but as the Arrow impossible theorem says, when it is limited to certain conditions, the perfect voting plan does not exist. So we hope to better the current model under reasonable assumptions.


[^1]: [Borda Count](https://en.wikipedia.org/wiki/Borda_count)

[^2]: Kenneth J. Arrow: A Difficulty in the concept of social welfare. In Journal of Political Economy
Vol. 58, No. 4 (Aug., 1950), pp. 328-346

[^3]: B steven j Paradoxes in Politics: An Introduction to the Nonobvious in Political Science

[^4]: Michel A. Habib and Alexander Ljungqvist  Firm Value and Managerial Incentives: A Stochastic Frontier Approach

[^5]: Charles R. Hadlock. • ”Discrete Mathematical Models: With Applications to Social, Biological, and Environmental. Problems” byFred Roberts. ...... Prentice-Hall, Englewood Cliffs, N.J., 1976.

[^6]: D.G.Saari  choice electration  Mathematics and Voting
