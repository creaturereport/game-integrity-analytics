# Game Integrity Analytics

**🎮 The Botting Threat Landscape**
Understanding how bad actors exploit game mechanics is the first step in building data queries to stop them. Here is a breakdown of the primary exploitation methods:

* **Macro Farmers:** Players download PC emulators (like BlueStacks) with built-in macro recorders to perfectly loop resource-collection sequences while they sleep.

* **Python Scripters:** Advanced cheaters use visual recognition libraries (like OpenCV) to script bots that "watch" the screen, identify buttons, and click automatically.

* **API Hijackers:** The most dangerous offenders bypass the visual game entirely, using network interceptors to send automated "resource collected" messages directly to the server thousands of times a second.

**🚜 Scenario 1: Hunting "Farm" Accounts**
In resource-driven mobile games, "Farm" accounts exist for one reason: to funnel massive amounts of stolen or botted resources to a main account. Finding the accounts bleeding the most resources is exactly how analysts hunt them down.

* **Phase 1: Exploratory Data Analysis (Testing the Hypothesis)**
Before building a complex model, I needed to test the waters. My first objective was to answer a foundational question: Are there players transferring highly abnormal amounts of resources immediately after the server's launch on July 1st, 2026?

```SQL
SELECT *
FROM `healthy-bonsai-231119.dragonfire_data.dragon_fire_Server194`
WHERE resources_transferred_out > 1000
ORDER BY resources_transferred_out DESC
LIMIT 100;
Phase 2: The Production Model (Automating the Hunt)
```
> Because the exploratory query returned massive hits of suspicious accounts, I could dive deeper into the investigation. I engineered a master SQL script designed to isolate resource-farming bot networks based on power-level stagnation and resource transfer anomalies.


**Query Objective:** Identify suspected resource-farming bots contributing to early server decay.
Target Criteria: Accounts with extreme resource transfers, zero power growth, and recent inactivity.

```SQL
SELECT 
    player_id,
    alliance_tag,
    account_creation_date,
    total_power_level,
    resources_transferred_out,
    days_since_last_login,
    CASE 
        WHEN resources_transferred_out > 500000 
             AND total_power_level < 1000 
             AND days_since_last_login > 7 
        THEN 'High Probability Bot'
        ELSE 'Standard Player'
    END AS integrity_flag
FROM 
    `healthy-bonsai-231119.dragonfire_data.dragon_fire_Server194`
WHERE 
    account_creation_date >= DATE_SUB(CURRENT_DATE(), INTERVAL 30 DAY)
ORDER BY 
    resources_transferred_out DESC;
```
**📊 Final Analysis & Business Impact:**

* **The Auto-Labeler (CASE Statement):** Manual scrolling is obsolete. This script automatically evaluates every player and creates a new integrity_flag column, instantly branding offenders as a 'High Probability Bot' so the security team can issue a targeted ban wave.

* **The Fresh Server Filter (DATE_SUB):** By restricting the data to accounts created within the last 30 days, this model specifically monitors rapid server decay on vulnerable, newly launched servers to protect new-player retention.
