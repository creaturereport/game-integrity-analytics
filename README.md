# game-integrity-analytics

**🤖 The Botting Threat Landscape**
Understanding how bad actors exploit game mechanics is the first step in building queries to stop them. Here is a breakdown of the primary exploitation methods:

* **Macro Farmers:** Players will download an emulator like BlueStacks or LDPlayer on their PC. These emulators have built-in macro recorders. A player just records themselves clicking through the sequence to collect resources, and then sets the macro to loop infinitely while they sleep.

* **Python Scripters:** More advanced cheaters will run the game in BlueStacks and write Python scripts using a tool called Appium (or visual recognition libraries like OpenCV). The script "watches" the screen, identifies buttons, and clicks them automatically.

* **API Hijackers:** The most dangerous cheaters don't even open the game visually. They use network interceptors to watch the hidden traffic between the game and the servers. Once they know the exact web address the game uses to say "Player X collected 100 wood," they write a script to send that exact message directly to the server thousands of times a second.

**Scenario 1 - Farm Accounts**
* Farm accounts exist for one reason: to funnel massive amounts of resources to a main account. Finding the accounts bleeding the most resources is exactly how you hunt them.

* Regular players transfer some resources to their alliance members, but bots transfer millions. Ordering it from highest to lowest, the bots will bubble straight to the top for analyses.

SQL script designed to parse mobile server population metrics and isolate resource-farming bot networks based on power-level stagnation and resource transfer anomalies
> To Test the waters, I wanted to answer my first question: are there any players who have outsources resources by more than 1,000 at the time of launch of the server? (July 1st, 2026)
Below, find my code that tests that thought process. 
```sql
SELECT *
FROM `healthy-bonsai-231119.dragonfire_data.dragon_fire_Server194`
WHERE resources_transferred_out > 1000
ORDER BY resources_transferred_out DESC
LIMIT 100;
```
Because I got a hit, I can now dive deeper in my analysis and investigation.
-- Query Objective: Identify suspected resource-farming bots contributing to server decay.
-- Target criteria: Accounts with high resource transfers, zero power growth, and recent inactivity.

```sql

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
    dragonfire_server_metrics
WHERE 
    account_creation_date >= DATE_SUB(CURRENT_DATE(), INTERVAL 30 DAY)
ORDER BY 
    resources_transferred_out DESC;
```
Final analysis:

The CASE Statement (The Auto-Labeler): Manually scrolling has been opted out by providing a master script that creates a brand-new column (integrity_flag) and automatically brands them as a 'High Probability Bot' or a 'Standard Player'.

The Date Filter (DATE_SUB): It restricts the data to only look at accounts created within the last 30 days, which is crucial for monitoring rapid server decay on a fresh server.
