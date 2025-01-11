![](src/Pasted%20image%2020250111220319.jpg)


[source](https://datalemur.com/sql-game) 

## Introduction

Welcome to SQL Squid Game – a free educational game that's inspired by Netflix's Squid Game, [SQL Murder Mystery](https://mystery.knightlab.com/), and a few other [SQL Games](https://datalemur.com/blog/games-to-learn-sql) we love to play.

You've just been hired as a Data Scientist by the mysterious Squid Game organization. The Front Man who runs the Squid Games promised you a fully remote job where you'll unlock cross-functional synergies with Generative AI. But in typical data industry fashion, you've been bait-and-switched. Turns out that the role is more about Product Analytics in SQL, and the job's not fully remote, it's hybrid: 5 days in office required, with 2 days optionally remote.

Before you can even complain on [r/DataScience](https://www.reddit.com/r/datascience/) about the gig, the Front Man puts a gun to your head and starts demanding answers to various business questions. You must write SQL queries to answer him – or else.![](src/Pasted%20image%2020250111220450.jpg)

---

[LEVEL 1](#LEVEL%201) 
[LEVEL 2](#LEVEL%202) 
[LEVEL 3](#LEVEL%203)
[LEVEL 4](#LEVEL%204)
[LEVEL 5](#LEVEL%205) 
[LEVEL 6](#LEVEL%206) 






---


# LEVEL 1

## Scenario

Welcome, Data Scientist! It's me, the Front Man. I see you've accepted my... unique employment opportunity.  
  
We've set up our operations on a private island – and before you ask, no, not _THAT_ kind of private island.  
  
You've joined just at the right time – the first game of Red Light, Green Light finished and we've generated some fresh data to analyze and fresh bodies to harvest.  

![Red Light Green Light Death](https://datalemur.com/sql-game/images/RedLightGreenLight.jpeg)

Your first challenge begins now:  
**The organizers want to identify vulnerable living players who might be easily manipulated for the next game. Find all players who are alive, in severe debt (debt > 400,000,000 won), and are either elderly (age > 65) OR have a vice of Gambling with no family connections.**

**SCHEMA**

![](src/Pasted%20image%2020250111220543.jpg)



```SQL
select * 
from player
where 
	1=1
	and status = 'alive'
	and debt > 400000000 
	and (age > 65 or (vice = 'Gambling' and has_close_family = 'false'))

```

![](src/Pasted%20image%2020250111220644.png)
# LEVEL 2

## Scenario

Ah, you've passed Level 1! I knew you had potential when you didn't flinch at our "player vulnerability metrics." I mean, really – what kind of Data Scientist can't handle identifying our most susceptible contestants?  
  
I mean, one of the last Data Scientists who worked here even suggested we "Not exploit vulnerable people." Can you imagine? I know I couldn't. Then again, neither can he now...  
  
Now for your next challenge:  
**The organizers need to calculate how many food portions to withhold to create the right amount of tension. In a table, calculate how many rations would feed 90% of the remaining(alive) non-insider players (rounded down), and in another column, indicate if the current rations supply is sufficient. (True or False)**  
  
Ready to make the players go hungry optimize resource allocation to improve operational efficiences?

![](src/Pasted%20image%2020250111220754.png)


```sql
SELECT round((
			SELECT COUNT(*) * 0.9
			FROM player
			WHERE STATUS = 'alive'
				AND isinsider = 'false'
			), 0) AS alive_count_90
	,CASE 
		WHEN rations.amount >= (
				SELECT COUNT(*) * 0.9
				FROM player
				WHERE STATUS = 'alive'
					AND isinsider = 'false'
				)
			THEN 'True'
		ELSE 'False'
		END AS sufficient_rations
FROM rations;



```

![](src/Pasted%20image%2020250111221045.png)

# LEVEL 3

## Scenario

Well, well! Look who's becoming quite the asset to our organization. Your resource optimization skills were... _chef's kiss_... absolutely delightful! The resulting chaos in the dormitory over the limited rations was quite the entertainment for our VIPs.

![Squid games dorm fight.](https://datalemur.com/sql-game/images/SquidGameDormFight-min.jpeg)

Speaking of entertainment, we're diving into some historical analytics today. The honeycomb game has always been a crowd favorite – there's something darkly mesmerizing about watching players take this childhood game so serious!  
  
Our meteorology department (yes we have that) has been meticulously tracking temperature data alongside player performance for decades. You see, we're not just sadistic- I mean, selective in our game design. There's actual science behind our methods!  
  
The organization is particularly interested in optimizing the difficulty levels based on seasonal variations. We noticed that a lot of players seem to use the strategy of melting the cookie during the hotter months. That's not very fair to our winter contestants, is it?

![Squid games dorm fight.](https://datalemur.com/sql-game/images/honeycomb_game.jpg)

Your next task:  
**Analyze the average completion times for each shape in the honeycomb game during the hottest and coldest months, using data from the past 20 years only. Order the results by average completion time.**


![](src/Pasted%20image%2020250111221147.png)
```SQL
with hottest_coldest_months as ( 
  -- 2: CTE definition starts
  SELECT month 
  -- 3: Select the month from the monthly_temperatures table
    FROM monthly_temperatures 
  -- 4: Filter for months with the maximum average temperature
    WHERE avg_temperature = (SELECT MAX(avg_temperature) FROM monthly_temperatures)
  -- 5: OR filter for months with the minimum average temperature
        OR avg_temperature = (SELECT MIN(avg_temperature) FROM monthly_temperatures)
  -- 6: CTE definition ends
    )
  -- 7: Main query starts
    select shape, extract(month from date) as month,avg(average_completion_time) avg_time
  -- 8: Select shape, month, and average completion time
    from honeycomb_game
  -- 9: Filter for records where the month is in the hottest_coldest_months CTE
    where  extract(month from date) in (select month from hottest_coldest_months )
  -- 10: Filter for records within the last 20 years
    and date >= current_date - interval '20 years'
  -- 11: Group by shape and month to calculate average completion time
    group by shape,month
  -- 12: Order the results by average completion time in ascending order
   order by avg_time
```

![](src/Pasted%20image%2020250111221419.png)

# LEVEL 4

## Scenario

Impressive work on the honeycomb analysis!  
You're proving to be quite the SQL mastermind. Three challenges in, and not a single ethical qualm in sight. I do love when our employees understand our... unique corporate culture. It's like a family, yknow?  
  
Now, let's get serious about team dynamics. The Tug of War game is approaching, and The Front Man has a special request before the game starts.

![Squid Games Tug of War](https://datalemur.com/sql-game/images/Tug_of_War.jpeg)

Between us, he's particularly interested in the... demographic advantages certain teams might have. Age does bring wisdom, but youth has its merits too, wouldn't you say?  
  
Your next challenge:  
**The Front Man needs to analyze and rank the teams before the Tug of War game begins. For each team that has exactly 10 players, calculate their average player age. Additionally, categorize the teams based on their average player age into three age groups:  
  
'Fit': Average age < 40  
'Grizzled': Average age between 40 and 50 (inclusive)  
'Elderly': Average age > 50  
  
Show the team_id, average age, age group, and rank the teams based on their average player age (highest average age = rank 1).**

![](src/Pasted%20image%2020250111221520.png)
```SQL
SELECT team_id
	,avg(age) avg_age
	,CASE 
		WHEN avg(age) < 40
			THEN 'Fit'
		WHEN avg(age) BETWEEN 40
				AND 50
			THEN 'Grizzled'
		ELSE 'Elderly'
		END age_group
	,rank() OVER (
		ORDER BY avg(age) DESC
		)
FROM player
GROUP BY team_id
HAVING count(team_id) = 10
```

![](src/Pasted%20image%2020250111221624.png)

# LEVEL 5

## Scenario

Your Tug of War analysis was just... just... SUBLIME!  
  
The way those "youngsters" marched to their doom, completely confident in their superior fitness and strength, **beatiful**- oh, pardon me. Sometimes data & death get me ~~horny~~ excited.  
  
But now... now we're going to do something really special. Something... intimate. Because what's better than analyzing cold, hard numbers? ANALYZING FRIENDSHIPS WE'RE ABOUT TO DESTROY WITH THE MARBLE GAME!

Your next challenge:  
**For the Marbles game, the Front Man needs you to discover who Player 456's closest companion is. First, find the player who has interacted with Player 456 the most frequently in daily activities. Then, confirm this player is still alive and return a row with both players' first names, and the number of interactions they've had.**

![](src/Pasted%20image%2020250111221651.jpg)
![](src/Pasted%20image%2020250111221701.png)

```SQL
-- CTE (Common Table Expression) to identify the player who interacts most frequently with player 456
WITH MostFrequentInteractions
AS (
	SELECT
		-- Determine the ID of the other player in the interaction
		CASE 
			WHEN player1_id = 456
				THEN player2_id -- If player 456 is player1, the other player is player2
			WHEN player2_id = 456
				THEN player1_id -- If player 456 is player2, the other player is player1
			END AS interacted_player_id
		,COUNT(*) AS interaction_count -- Count the number of interactions for each player
	FROM Daily_Interactions -- The table containing all daily interactions
	WHERE player1_id = 456
		OR player2_id = 456 -- Filter rows where player 456 is involved in the interaction
	GROUP BY interacted_player_id -- Group by the other player ID
	ORDER BY interaction_count DESC -- Order by interaction count in descending order
		LIMIT 1 -- Select only the most frequently interacted player
	)
-- Final query to retrieve player details and interaction count
SELECT p1.first_name AS player_456_name
	,-- Get the name of player 456
	p2.first_name AS companion_name
	,-- Get the name of the most frequently interacted companion
	mfi.interaction_count -- Include the interaction count
FROM MostFrequentInteractions mfi -- Use the CTE to get the most frequent interaction
JOIN player p1 ON p1.id = 456 -- Join to get player 456's details
JOIN player p2 ON p2.id = mfi.interacted_player_id -- Join to get the most frequently interacted player's details
WHERE p2.STATUS = 'alive';-- Ensure the companion player is still alive

```

**CTE Result**

![](src/Pasted%20image%2020250111222049.png)

Final Query

![](src/Pasted%20image%2020250111222220.png)

# LEVEL 6

## Scenario

Your work and queries responsible for finding relationship information was of great help in the last task. It was thanks to you we were able to get rid of the pesky friendships that often plague these games...  
  
Now onto another equally fun topic as destroying lives: QUALITY CONTROL.  
  
You know, some might say we're being a bit... excessive with our equipment standards. But tell me, what's worse: A guard's mask falling off at the wrong moment, or a gun malfunctioning?  
  
BOTH! BOTH ARE EQUALLY PROBLEMATIC and require a few extra casualities.

![Squid Games Tug of War](https://datalemur.com/sql-game/images/SquidGamesEquipment.jpeg)

Our suppliers have been getting a bit... sloppy lately. We need to start giving some "friendly" reminders to some of our already nervous suppliers.  
  
Your next challenge:  
**The guards need to track equipment durability across games. They've noticed some equipment breaking too early and want to identify potential quality issues. Find the supplier who produces the most faulty equipment from the most faulty game type and output the average lifespan of this supplier's equipment (in years(365.2425), whole numbers only).**

![](src/Pasted%20image%2020250111222303.png)

 **Most faulty game type**
```sql
  SELECT e.game_type
    FROM equipment e
    JOIN failure_incidents fi ON e.id = fi.failed_equipment_id
    GROUP BY e.game_type
    ORDER BY COUNT(*) DESC
    LIMIT 1
```

![](src/Pasted%20image%2020250111222444.png)


 **Supplier who produces the most faulty equipment**
 
```sql
WITH MostFailedGameType AS (
    SELECT e.game_type
    FROM equipment e
    JOIN failure_incidents fi ON e.id = fi.failed_equipment_id
    GROUP BY e.game_type
    ORDER BY COUNT(*) DESC
    LIMIT 1
)

    SELECT e.supplier_id,count(*)
    FROM equipment e
    JOIN failure_incidents fi ON e.id = fi.failed_equipment_id
    WHERE e.game_type = (SELECT game_type FROM MostFailedGameType)
    GROUP BY e.supplier_id
    ORDER BY COUNT(*) DESC
    LIMIT 1
```

![](src/Pasted%20image%2020250111222614.png)
Final Query

```sql
WITH MostFailedGameType AS (
    SELECT e.game_type
    FROM equipment e
    JOIN failure_incidents fi ON e.id = fi.failed_equipment_id
    GROUP BY e.game_type
    ORDER BY COUNT(*) DESC
    LIMIT 1
),
WorstSupplier AS (
    SELECT e.supplier_id
    FROM equipment e
    JOIN failure_incidents fi ON e.id = fi.failed_equipment_id
    WHERE e.game_type = (SELECT game_type FROM MostFailedGameType)
    GROUP BY e.supplier_id
    ORDER BY COUNT(*) DESC
    LIMIT 1
)
SELECT 
    FLOOR(AVG((fi.failure_date - e.installation_date) / 365.2425)) AS avg_lifespan_years
FROM equipment e
JOIN failure_incidents fi ON e.id = fi.failed_equipment_id
WHERE e.supplier_id = (SELECT supplier_id FROM WorstSupplier);
```

![](src/Pasted%20image%2020250111222704.png)
