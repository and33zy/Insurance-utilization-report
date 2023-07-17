# Insurance-utilization-report
Claim utilization report generated using SQL on OpenDental. 
/*claim count by insurance carrier for date range with sum of fees, estimates and paid amounts*/
SET @FromDate='2021-10-01' , @ToDate='2022-09-30';
SELECT
ca.CarrierName,
COUNT(cl.ClaimNum) AS '#Claims',
FORMAT(100*(COUNT(cl.ClaimNum)/(SELECT COUNT(claim.ClaimNum) FROM claim WHERE DateService BETWEEN @FromDate AND @ToDate)), 2) AS '%Claims',
SUM(cl.ClaimFee) AS '$ClaimFees',
SUM(cl.InsPayEst) AS '$InsPayEst',
SUM(cl.InsPayAmt) AS '$InsPaidAmt'
FROM claim cl
INNER JOIN insplan i
ON i.PlanNum = cl.PlanNum
INNER JOIN carrier ca
ON ca.CarrierNum = i.CarrierNum
WHERE cl.ClaimType <> 'PreAuth'
AND (cl.ClaimStatus = 'R' OR cl.ClaimStatus = 'S')
AND (DateService BETWEEN @FromDate AND @ToDate)
GROUP BY ca.CarrierName

Sorted by name, includes all insurance companies and plans utilized by the office. 

Depicts the number of claims, estimation of payouts by claims, and actual payouts of claims. 

Claim fees are are taken out from the office to sent claims electronically using ClaimConnect as a portal through OpenDental to DentalXChange.
