# game-integrity-analytics

**🤖 The Botting Threat Landscape**
Understanding how bad actors exploit game mechanics is the first step in building queries to stop them. Here is a breakdown of the primary exploitation methods:

* **Macro Farmers:** Players will download an emulator like BlueStacks or LDPlayer on their PC. These emulators have built-in macro recorders. A player just records themselves clicking through the sequence to collect resources, and then sets the macro to loop infinitely while they sleep.

* **Python Scripters:** More advanced cheaters will run the game in BlueStacks and write Python scripts using a tool called Appium (or visual recognition libraries like OpenCV). The script "watches" the screen, identifies buttons, and clicks them automatically.

* **API Hijackers:** The most dangerous cheaters don't even open the game visually. They use network interceptors to watch the hidden traffic between the game and the servers. Once they know the exact web address the game uses to say "Player X collected 100 wood," they write a script to send that exact message directly to the server thousands of times a second.

SQL script designed to parse mobile server population metrics and isolate resource-farming bot networks based on power-level stagnation and resource transfer anomalies

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
