---
description: >-
  This page describes the system of mutual node assessment implemented in the
  Gravity Protocol for reputation management.
---

# Gravity Reputation Management

## GravityScore UpdateFlow

In the Gravity network, node ratings are calculated in the internal ledger and depend on mutual assessments of nodes.

The following entities are involved in the rating update process:

* _GravityScore_ represents a node's rating. It is being updated in the Gravity ledger and then fed into the target blockchains.
* _p2pScores_ is an object that contains a mutual evaluation of nodes.
* _Behaviour_ is a value calculated by a node for another participant that contains information about its behaviour for a specific period of time.

These objects participate in the following sequence of actors' actions:

![](https://drive.google.com/uc?export=view&id=1LgmbmtKYMhVtDE6tORZsRtyuYPhguP4S)

  
and more detailed entity relationship

![](https://drive.google.com/uc?export=view&id=12FB3eHWLfApOD069fOP2xqXXlS5DpkNC)

  
  
Each actor is on a different timeline:

![](https://drive.google.com/uc?export=view&id=1NhNtnCSJxLos7_qcEreZVi9mOsg1LuQ0)

## **Observation of other nodes**

Each node collects information known to it about the behaviour of other participants. Example of an action that is being monitored:

* Feed accuracy
* Nebula update
* System contract update
* Gravity ledger update
* Initial locked amount

The node records the number of successful and unsuccessful actions for each known participant.

**Behaviour Calculation**

After the time specified in the settings has expired, the node calculates the $$Behaviour$$ vector containing the opinion about the behavior of all participants.

The $$i$$node in relation to the $$j$$node executes calculations, summing up the elementary actions:

$$
Behaviour_{ij} = \sum feedAccuracy_{ij} + \sum nebulaUpdate_{ij} + \ ...\  +\sum ledgerUpdate_{ij}
$$

where the weight of positive and negative actions is given in a tabular format

| Action | + | - |
| :--- | :--- | :--- |
| Feed accuracy | $$postiveActionWeight_{feed}$$ | $$negativeActionWeight_{feed}$$ |
| Nebula update | $$postiveActionWeight_{nebula}$$ | $$negativeActionWeight_{nebula}$$ |
| Gravity ledger update | $$postiveActionWeight_{gravity}$$ | $$negativeActionWeight_{gravity}$$ |

Example: if a node has sent correct data 3 times during observation and made a mistake once, the resulting sum is $$ Behabiour_{ij} = \sum feedAccuracy_{ij} = 3 \cdot postiveActionWeight{feed} - 1 \cdot negativeActionWeight{feed} $$.

#### **Update p2pScore**

After calculating $$Behaviour$$, the node determines the value of p2pScore change

![](https://drive.google.com/uc?export=view&id=12avTGiXp7GI5q9AXYuq9f3BHLaPkqsYq)

If $$behaviour_{ij,height} \in increaseRange:$$

$$ p2pScore{ij} = p2pScore{ij,height-1} + \Delta_{increase} $$

If $$behaviour_{ij,height} \in unchangeRange:$$

$$ p2pScore{ij} = p2pScore{ij,height-1} $$

If $$behaviour_{ij,height} \in decreaseRange:$$

$$
p2pScore_{ij} = p2pScore_{ij,height-1} - repeatFail(badResultCount_{ij},funcDecrease(t))*\Delta_{decrease}
$$

$$repeatFail()$$ - downgrade function

$$badResultCount$$ - count of recent negative verdicts $$funcDecrease(t)$$ - declining function in time 

If $$behaviour_{ij,height} \in banRange:$$

$$p2pScores_{ij} = 0$$

After the $$p2pScore$$ update, the node sends the transaction with the actual data to the Gravity ledger.

**Update GravityScore in Gravity Ledger**

The Round Leader updates the rating when creating the block by calculating each participant's rating according to the EigenTrust algorithm and actual p2pScores values:

$$
GravityScore_{height} = updateGravityScore(p2pScores)
$$

Updating the GravityScore in the Gravity ledger is a costless operation, so the frequency of updates of the rating can be chosen arbitrarily, for example, the minimum possible interval can be the time of block creation.

**Update GravityScore in System Contract**

The updated ratings are transferred by participants to SystemContract in target chains.

In contrast to internal rankings updates, frequent upgrades in target chain can be costly. In order to reduce costs, a mixed approach can be used: an infrequent fixed period of rating update \(1h,6h, etc.\) and an update that triggers a significant change of participants' GravityScore.

