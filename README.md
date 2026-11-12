# Flormar Mobile App: Enhancing Profile Flows & CRM Data Quality (Product Case Study)

An independent product management and UX analysis exploring opportunities to optimize user onboarding, profile data synchronization, and CRM segmentation within the Flormar mobile application.

## 📌 Overview
During a product teardown of the Flormar mobile application, I identified an optimization opportunity within the profile update and onboarding flows. This case study outlines the discovery, analytical diagnostic, strategic phased solution, and expected business impact.

## 🔑 Key Findings & UX Insights
* **The Feedback Gap:** Users can update their birth date in the "My Profile" (*Profilim*) section, but tapping "Save" (*Kaydet*) triggers no visual confirmation (toast message or pop-up).
* **State & Data Consistency:** The update process encounters a synchronization friction, leading to a silent failure where the backend database may not reliably persist the new value.
* **CRM & Business Impact:** Inaccurate or missing birth date data degrades personalized marketing campaigns (e.g., automated birthday discounts) and erodes long-term user trust.

---

## 💡 Strategic Solution: 3-Phase Product Roadmap

| Phase | Scope | Objective |
| :--- | :--- | :--- |
| **Phase 1 (UX & Feedback)** | Optimize the profile update API/state persistence and implement clear UI confirmation toast notifications upon saving. | Increase user control, transparency, and data reliability. |
| **Phase 2 (Onboarding)** | Introduce an *optional* Birth Date field during initial sign-up with value-driven micro-copy. | Capture clean CRM data early without hurting registration rates. |
| **Phase 3 (Data Hygiene)** | Audit existing profile records to refine CRM segmentation rules and ensure accurate campaign targeting. | Maximize marketing ROI and prevent irrelevant notifications. |

---

## 🛠️ SQL Diagnostics & Data Analysis
To evaluate birth date distribution and detect default or missing value anomalies in a production database, the following diagnostic query was designed:

```sql
-- Analyzing birth date distribution to support CRM data hygiene and segmentation
SELECT 
    birth_date,
    COUNT(*) AS total_users,
    ROUND(100.0 * COUNT(*) / SUM(COUNT(*)) OVER (), 2) AS percentage_of_total
FROM user_profiles
WHERE birth_date IS NOT NULL
GROUP BY birth_date
ORDER BY total_users DESC
LIMIT 10;
