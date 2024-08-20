# Bitcoin Fee Calculator Docs

## Rollup Fee Simulation Documentation

### **Introduction**

This document provides a detailed explanation of how to calculate the required fee rate for including rollup batches in a blockchain, given a specific number of rollups and base fee rate. It also explains how to convert the calculated fee into daily and yearly costs.

Formulas

| **Variable**    | **Description**                                                         |
| --------------- | ----------------------------------------------------------------------- |
| totalDemand     | The total transaction size trying to inscribe on that particular block. |
| rollupBatchSize | Batch size of the rollup transaction.                                   |
| numRollups      | The number of rollups competing to inscribe on that block.              |
| maxRollupSpace  | The space in the block that rollups compete to inscribe on.             |
| maxSize         | ——                                                                      |
| frequencyPerDay | Frequency of inscription to L1.                                         |

### 1. **Total Demand**

The total demand is the combined size of all rollup batches that need to be included in a block.

$$totalDemand = rollupBatchSize * numRollups$$

### 2. Max Rollup Space

This is the maximum space available for rollup batches in a block.

$$maxRollupSpace = maxSize (KB) * 1024$$

**(to convert to bytes)**

### 3. Competition Factor

The competition factor adjusts the base fee rate based on the ratio of total demand to maximum rollup space. If the total demand exceeds the available space, the competition factor increases.

$competitionFactor = max(1, totalDemand / maxRollupSpace)$

### 4. Required Fee Rate

This is the fee rate needed per byte to ensure the inclusion of the rollup batches.

![output.png](Bitcoin%20Fee%20Calculator%20Docs%2044cc8157c2be4924b3900e5af32e6d6c/output.png)

$$requiredFeeRate = baseFeeRate * competitionFactor * 1.1$$

1.1x, is the multiplier factor to have 10% excess priority fees that is added to the base fee, to assure our inscription is included in the block.

### 5. Total Fee

The total fee is calculated by multiplying the required fee rate by the rollup batch size.

Bitcoin transactions are measured in virtual bytes (vB). Since SegWit, 1 vB is approximately 4 times smaller than a non-SegWit byte.

- 1 kB = 1,000 bytes = 250 vB (assuming average SegWit usage).

$$totalFee=requiredFeeRate * (rollupBatchSize(in KB) / 4)$$

### 6. Convert Fee to Per Day

To convert the total fee to a daily fee, we multiply by the number of transactions expected per day.

$$perDayFee=totalFee * frequencyPerDay$$

​

### 7. Convert Fee to Per Year

The yearly fee is calculated by multiplying the daily fee by the number of days in a year (365).

$$perYearFee=perDayFee * 365$$

## Example Calculation

Given:

**rollup_batch_size = 400 KB = 409600 bytes**

**numRollups = 10**

**baseFeeRate = 3 sats per byte**

**frequency = 24 transactions per day (1 every hour)**

**max_rollup_space = 400 KB = 409600 bytes**

### Step-by-Step Calculation:

**Total Demand:**

total_demand = 409600 bytes × 10 = 4096000 bytes (or 4000 KB)

**Max Rollup Space:**

max_rollup_space = 409600 bytes (or 400KB)

**Competition Factor:**

$$
competitionFactor=max(1,4096000/409600)=max(1,10)=10
$$

**Required Fee Rate:**

$$requiredFeeRate = 3 * 10 * 1.1 = 33(satoshis-per-byte)$$

**Total Fee:**

$$totalFee = 33 * (409600 / 4) = 3379200(satoshis)$$

**Convert to Per Day:**

$$perDayFee = 3379200 * 24 = 81100800(Satoshis-per-day)$$

**Convert to Per Year:**

$$perYearFee = 324403200 * 365 = 29601792000(satoshis-per-year)$$

## Final Results:

**Total Fee per Transaction: 3379200 satoshis**

**Daily Fee: 81100800 satoshis**

**Yearly Fee: 29601792000 satoshis**

At current price of sats at **0.000607 USD**

This equates to

Total Fee in USD per transaction: **2,051.31 USD**

Daily Fee in USD : **49,228 USD**

Yearly Fee in USD: **17.97M USD**

![fee_graph.png](Bitcoin%20Fee%20Calculator%20Docs%2044cc8157c2be4924b3900e5af32e6d6c/fee_graph.png)

As the number of transactions increases on L2 the fee paid for each transaction reduces.
