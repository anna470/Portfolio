# Portfolio

## Welcome to Anna's Portfolio

**Scaling Smart: Revenue and Performance Analysis**

Which treatments bring in the most revenue, which treatments should we potentially focus on specialisation 

```
SELECT  t.treatment_type,
    ROUND (SUM (b.amount) ,2) AS amount_by_treatment
FROM data-analytic-bootcamp-484509.hospital_managment.billing AS b
     INNER JOIN data-analytic-bootcamp-484509.hospital_managment.treatments AS t
          On t.treatment_id = b.treatment_id
GROUP BY t.treatment_type
ORDER BY amount_by_treatment DESC;
```

On table 1 we can see the income through the different treatments we offer over the year 2023. It suggests that in our efforts to specialise, we should look at Chemotherapy and scanning services like MRI and X-ray. These are high cost treatments and therefore bring in a high revenue. 

—--------------------------------------------------------------------------------------------------------
Which specialisation brings in the most revenue and therefore should receive extra investment 

```
SELECT  d.specialization,
    ROUND (SUM (b.amount) ,2) AS amount_by_specialization
FROM data-analytic-bootcamp-484509.hospital_managment.doctors AS d
    INNER JOIN data-analytic-bootcamp-484509.hospital_managment.appointments AS a
         ON d.doctor_id = a.doctor_id
    INNER JOIN data-analytic-bootcamp-484509.hospital_managment.treatments AS t
          ON  a.appointment_id = t.appointment_id        
    INNER JOIN data-analytic-bootcamp-484509.hospital_managment.billing AS b
         ON b.treatment_id = t.treatment_id
GROUP BY specialization
ORDER BY amount_by_specialization DESC;
```

—------------------

```
SELECT  d.specialization,
        COUNT (*) AS appointment_numbers
FROM data-analytic-bootcamp-484509.hospital_managment.doctors AS d
    INNER JOIN data-analytic-bootcamp-484509.hospital_managment.appointments AS a
         ON d.doctor_id = a.doctor_id    
    INNER JOIN data-analytic-bootcamp-484509.hospital_managment.billing AS b
         ON b.patient_id = a.patient_id
GROUP BY specialization
ORDER BY COUNT (*) DESC;
```

On table 2 and 3  we can see that in the year 2023 Pediatrics  was the most used specialisation, in cost and by appointment numbers. This suggests that we should also focus on pediatrics.  

—----------------------------------------------------------------------------------

Which Dr.s bring in the most revenue?

```
SELECT  d.doctor_id,
       CONCAT('Dr.', ' ', d.last_name) AS dr_name,
    ROUND (SUM (b.amount) ,2) AS amount_by_doctor
FROM data-analytic-bootcamp-484509.hospital_managment.doctors AS d
    INNER JOIN data-analytic-bootcamp-484509.hospital_managment.appointments AS a
         ON d.doctor_id = a.doctor_id    
    INNER JOIN data-analytic-bootcamp-484509.hospital_managment.billing AS b
         ON b.patient_id = a.patient_id
GROUP BY d.doctor_id,
        d.first_name,
        d.last_name
ORDER BY amount_by_doctor DESC;
```

All our Doctors bring in the same amount, which we can see on table 4. This shows us a well balanced income, we should ensure that all our doctors are compensated accordingly. 

—--------------------------------------------------------------------------------------------------------
Which patients are high value and should use target retention strategies  for?
```
SELECT p.patient_id,
        CONCAT(p.first_name, ' ', p.last_name) AS full_name,
       ROUND (SUM (b.amount),2) AS complete_amount,
FROM data-analytic-bootcamp-484509.hospital_managment.billing AS b
     INNER JOIN data-analytic-bootcamp-484509.hospital_managment.patients AS p
          On p.patient_id = b.patient_id
GROUP BY P.patient_id, full_name
HAVING complete_amount >10000
ORDER BY complete_amount DESC;
```
Laura Davis is our top client, we should ensure she is happy with the treatments she receives and if she has any concerns that we can help clear up, so she will keep choosing our facilities for her treatments 

—----------------------------------------------------------------------------------

Which insurance providers do we offer perks to, in order to retain/ increase the amount of patients sent to us?

```
SELECT p.insurance_provider,
       ROUND (SUM (b.amount),2) AS complete_amount,
FROM data-analytic-bootcamp-484509.hospital_managment.billing AS b
     INNER JOIN data-analytic-bootcamp-484509.hospital_managment.patients AS p
          On p.patient_id = b.patient_id
GROUP BY p.insurance_provider
ORDER BY complete_amount DESC;
```
—------------------


```
SELECT p.insurance_provider,
       ROUND(SUM (b.amount) / (SELECT SUM(amount), FROM data-analytic-bootcamp-484509.hospital_managment.billing) *100,2) AS percent_amount,
FROM data-analytic-bootcamp-484509.hospital_managment.billing AS b
     INNER JOIN data-analytic-bootcamp-484509.hospital_managment.patients AS p
          On p.patient_id = b.patient_id
GROUP BY p.insurance_provider
ORDER BY percent_amount DESC;
```

On Table 7 we can see Medicare Plus is the Insurance used by 43.7% of our patients, our retention strategies should be focused on them whereas HealthIndia should be in focus for our strategies to increase patient referrals. On table 6 we can see the actual revenue made through each insurance. 
 
—--------------------------------------------------------------------------------------------------------

Which treatments have had the most growth over the last  12 months - where do we need to invest?
```
SELECT EXTRACT(month FROM treatment_date) AS month,
      COUNT (treatment_type) AS treatments,
      ROUND (SUM(amount), 2) AS amount
FROM data-analytic-bootcamp-484509.hospital_managment.treatments AS t
    INNER JOIN data-analytic-bootcamp-484509.hospital_managment.billing AS b
              ON t.treatment_id = b.treatment_id
GROUP BY month
ORDER BY month;
```
On table 8 we can see our revenue fluctuate during the year 2023 with surprising dips during the winter months February and December which could indicate a lack of Quality in our respiratory department which requires investigation. 

—--------------------------------------------------------------------------------------------------------

How balanced is the work load between dr.’s?

```
SELECT CONCAT('Dr.' ' ', d.last_name) AS Dr_name,
       COUNT (a.appointment_id) AS appointments,
       CASE
       WHEN COUNT (a.appointment_id) <= 15 THEN 'underbooked'
       WHEN COUNT (a.appointment_id) <= 20 THEN 'balanced'
      ELSE 'overbooked'
      END AS level
FROM data-analytic-bootcamp-484509.hospital_managment.doctors AS d
    INNER JOIN data-analytic-bootcamp-484509.hospital_managment.appointments AS a
              ON d.doctor_id = a.doctor_id
GROUP BY d.Doctor_id, (CONCAT('Dr.' ' ', d.last_name))
ORDER By COUNT (a.appointment_id);
```

In Table  11  we can see how well our doctors are utilised. We can see an unbalance here, this might be related to the different specialisations and the therefore different length of appointment. However this requires looking into in order to avoid risks. 

—-----------------------------------------------------------------------------------------------------

What is the impact of no-shows and how affected are each specialisations?

```
SELECT treatment_type,
      ROUND(SUM(b.amount)) AS loss,
      a.status,
FROM data-analytic-bootcamp-484509.hospital_managment.appointments AS a
    INNER JOIN data-analytic-bootcamp-484509.hospital_managment.billing AS b
              ON a.patient_id = b.patient_id
    INNER JOIN data-analytic-bootcamp-484509.hospital_managment.treatments AS t
              ON a.appointment_id = t.appointment_id
WHERE a.status = 'No-show'
GROUP BY a.status, treatment_type;
```
—-------------------------

```
SELECT ROUND(SUM(b.amount)) AS loss,
      A.status,
FROM data-analytic-bootcamp-484509.hospital_managment.appointments AS a
    INNER JOIN data-analytic-bootcamp-484509.hospital_managment.billing AS b
              ON a.patient_id = b.patient_id
WHERE a.status = 'No-show'
GROUP BY a.status;
```

In the different specialisations the Impact is for  Physiotherapy 147513.00 for ECG 110006.00, Chemotherapy 134576.00,  MRI 157258.00 and   X-Ray 231072.00 Overall there is a loss of 780424.00. 
—--------------------------------------------------------------------------------------------------------

Are we making a profit or loss?

```
SELECT DISTINCT treatment_type, b.amount - t.cost AS profit,
FROM data-analytic-bootcamp-484509.hospital_managment.billing AS b
    INNER JOIN data-analytic-bootcamp-484509.hospital_managment.treatments AS t
          ON b.treatment_id = t.treatment_id;
```

As of now we do not make a profit or loss on all treatments.  It is recommended to review strategies to start making a profit . 

What is our ROI? 

```
SELECT DISTINCT treatment_type,  
ROUND(AVG ((b.amount - t.cost) / t.cost)) AS ROI,
FROM data-analytic-bootcamp-484509.hospital_managment.billing AS b
    INNER JOIN data-analytic-bootcamp-484509.hospital_managment.treatments AS t
          ON b.treatment_id = t.treatment_id
GROUP BY TREATMENT_TYPE;
```

Our ROI therefore is also 0. 
—--------------------------------------------------------------------------------------------------------
