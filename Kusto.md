# Kusto Query Language (KQL)

## Useful Links & Resources

[Log Analytics Demo Environnment](https://aka.ms/lademo)

[Pluralsight Intro to KQL Course](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)

[Tech Community KQL Cheatsheet](https://techcommunity.microsoft.com/t5/azure-data-explorer-blog/azure-data-explorer-kql-cheat-sheets/ba-p/1057404)

## Search 

Before we begin, you will often see the **take** operator used with the example queries. The take operator is **used to specify the amount of events or rows you want returned**. The caveat is that it'll be a ***randomized set of rows*** depending on your conditions that were set beforehand.  

The first operator we'll review is **search**. You'll often find yourself using search to conduct a generalized search across all columns where you aren't sure of the conditions to set in your query to limit the result set or build a more effective and efficient query. This can be useful if you don't know what table to use and/or the column. 

**EXAMPLE 1**

Without a table

![search without table](https://github.com/elmatthias33/KQL/assets/138261985/67c89fb3-bac5-443a-b3ab-b97b8b4e819b)


> If you don't know the table name, you'll often search without one. Once you have your results, you'll want to read through them until you find events that you think match or are correlated to what you searched.  From there, you'll want to mentally make note of the table name (under the $table column), the column that contains whatever you searched and any other columns that you think would be useful when you're building your query. You can use the information you've collected as a foundation for building a better query

**EXAMPLE 2**

With a table

![search with table](https://github.com/elmatthias33/Kusto/assets/138261985/6a7390ca-18f9-4956-a496-9887a869ce89)

> Same as above, once you have your results, you'll want to read through them until you find events that you think match or are correlated to what you searched.  The difference here is you'll want to mentally make note of the column that contains whatever you searched and any other columns that you think would be useful when you're building your query. Use that information to start setting conditions or building a more efficient query. 

## Where

After you've at least determined your table, you'll want to start limiting your result set. That's what the **where** operator accomplishes. It's similar to search but the main difference is you're ***limiting your results based on conditions***. The where operator is probably the most common and potentially most important operator that you'll use with KQL. 

There's several other string operators or filtering functions you'll use with **where**. Pick the one that best fits your use case. 

| Operator | Description | Example |
| ------------- | --------------- | --------------------- | 
| == | Means equals. This is case sensitive  | where eventID == 4625
| != | Means does not equals. This is case sensitive | where eventID != 4625 | 
| =~ | Also means equals but it's not case sensitive | where Account =~ "john Doe" |
| contains | Searches for events containing a case-insenstive string | where Account contains "John Doe" |
| !contains | Searches for events where the string does not contain your searched value  | where Account !contains "John Doe" |
| in | Means equal to any elements | where EventID in ("4720", "4625", "4624")  or where Account in(Watchlist|
| !in | Means not equals to any of the elements | where Account !in (Watchlist) |
| isnnotempty | Searches for events where the specified column is not empty | where isnotempty(Account) |

For additional string operators, view [String Operators](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/datatypes-string-operators#what-is-a-term)

**EXAMPLE 1**

![where equals](https://github.com/elmatthias33/Kusto/assets/138261985/12d71d0f-2b22-4405-9563-5c13ff65bd0c)

Query Explanation:

>The query searches for events where the value or string in the EventID column equals 4625. If you're using == it needs to be an exact match to the value/string. You can't use == "john doe" if the string you're looking for is "John Doe". 

**EXAMPLE 2**

![where contains](https://github.com/elmatthias33/Kusto/assets/138261985/adf0813c-c78c-42ee-a64b-f228f73f6019)

Query Explanation:

>The query searches for events where the value or string in the Account column contains NT Authority. Now let's say the string is "nt authority \ System". With contains you'll still receive results with that string value. If you used contains_cs (CS means case sensitive) or another operator such as **has**, you won't see any results with that string because it doesnn't exactly match the conditions.

**EXAMPLE 3**

![where !in](https://github.com/elmatthias33/Kusto/assets/138261985/cff0294e-9443-4777-a101-ceceaaf753ca)

Query Explanation:

>Let statements will be dicussed later on but in this instance, it was used to call our watchlist and append the syntax to "watchlist". That watchlist contains a list of user account information. With the example above, we'd be querying for events where the value or string from the AccountUPN column doesn't match to a string in the watchlist.

## Project
When you think of the project operator, think of projection or projector. When you're using a projector, you want to see an output of what you input. The same concept applies to the project operator. You're saying you want to see an output of the columns that you input.  Let's say there's 10 columns, if you put `TableName | project Account`, your result set will only include rows from the Account Column. 

There are also several different project operators. Below are examples of a few. 

**EXAMPLE 1**

![kql project](https://github.com/elmatthias33/Kusto/assets/138261985/5d0a60b6-447c-42fe-ba99-5bc3d5524f88)

Query Explanation:

> As you see in the query above, our result set only includes the three columns listed after the project operator. All together, you're saying you want 10 rows from the SecurityEvent table and you only want to see results from those three columns. 

**EXAMPLE 2**

![kql project-reorder](https://github.com/elmatthias33/Kusto/assets/138261985/56649c5b-d4e7-4c46-8e69-709350c58040)

> Unlike the solo project operator, the project-reorder operator doesn't display only the columns you input. Depending on previous conditions in your query, it'll display all the columns that it would have if you didn't use project, but it adjust the order of the columns. Let's say you ran a query without using any of the project operators and your result set had 11 columns and Account was at the end (or far right) of your list of columns and your Activity column was halfway down. If you used project-reorder, it'll leave the other 9 columns in your result set but Account and Activity will be at the start of the list of columns. Basically if you wrote `| project-reorder Account, Activity`, Account would now be the first column and Activity the second. Whatever order you write your columns in, that's the order your results will display in 

**EXAMPLE 3**

![kql project-away](https://github.com/elmatthias33/Kusto/assets/138261985/c0c189fb-8823-49b6-9f7d-d2209c20d3c6)

> Just like with project-reorder, project-away keeps columns that would've been there if you didn't use any project operators. The difference with this operator is that it removes the columns that you listed, so if you originally had 8 columns and you decided to use `| project-away Account`, your result set will now show 7 columns because the Account column was removed.

**Take note that if you use project early on in your query, you can no longer use the columns that you didn't list later on in your query**. What this means is if you were to use `SecurityEvent | project TimeGenerated, Account, EventID`, you can't go to any of the following lines in the sequence and query for something like the Activity or Computer column. You would have to add those columns to your project if you want to use them later. This is the case for several other operators that we'll discuss later. 

## Time

Time is not an operator in KQL, but in this section we'll briefly dicuss how to set the time in your query. Of course at the top of your query page you'll see the option to set the time range but if you're learning KQL, it's good practice to learn how to set that in the query yourself. 

| Value | Translation |
| --------- | --------- |
| ago(1ms) | Translates to 1 millisecond ago |
| ago(1s) | Translates to 1 second ago |
| ago(5m) | Translates to 5 minutes ago |
| ago(10h) | Translates to 10 hours ago |
| ago (7d) | Translates to 7 days ago | 
| between (datetime(7/2/2023, 00:30:00.649 PM) ..now()) | Translates to between the specified time and now | 
| between (datetime(7/2/2023, 00:30:00.649 PM) ..datetime(7/7/2023, 00:30:00.649 PM)) | Translates to between those specified times |

**EXAMPLE 1**

![kql where time](https://github.com/elmatthias33/Kusto/assets/138261985/50659b52-2498-4a70-b9ba-22fcb9a56569)

Query Explanation

>You'll notice that we used >=. Oddly enough it doesn't mean greater than or equal to the time that we entered. The query means that we're searching for events where the TimeGenerated is within the last 3 hours. 

**EXAMPLE 2**

![kql where datetime](https://github.com/elmatthias33/Kusto/assets/138261985/8b297295-dbc6-4c26-a91f-5c4deed7773d)

Query Explanation

>Same as above, we used >=. The query means that we're searching for events where the TimeGenerated is between 30 June and now(the time that you ran the query).

## Distinct

Whenever you see distinct, think deduplication. It gets rid of duplicates values from your columns. If you add more columns to the distinct operator, it looks for unique combinations. The examples below will demonstrate the difference. 

**EXAMPLE 1**

![distinct kql](https://github.com/elmatthias33/Kusto/assets/138261985/0bfdea99-0e88-4f2e-bb0a-4e91ca176ae4)


>As you see in the screenshot above, we used distinct to get a deduplicated list of EventIDs from the SecurityEvent table.  

**EXAMPLE 2**

![distinct kql v2](https://github.com/elmatthias33/Kusto/assets/138261985/5e78e6ac-3b1e-4a02-aea9-6536eb21399d)

> The difference with this example is we've added another column. The first example returned less than 30 rows but this query returned over 200 rows. That's because in this case, there were different computers that had logs containing different EventIDs. If we were to add a third column such as TimeGenerated, we could see even more rows returned because those computers could have logs containing the same EventIDs at different times during the specified time period. So again, think of distinct as ***getting rid of duplicates or showing unique combinations as you add more columns***. 

## Checkpoint 1

Checkpoints will be used to test some of the operators that we've reviewed so far. In order to follow along, you'll need access to a personal log analytics workspace that has logs ingested or the log analytics demo workspace. Below you'll find some mock use cases or examples where you'll have to use the previous operators to build a query that would fulfill the requirements of that use case. 

**Use Case 1** (can be used with LogA demo workspace)

We would like to know if we have logs that show when users have successfully logged onto windows devices. We know the activity is "An account has successfully logged on" but we don't know the table, the eventID or any other information. Can you query for a result set that only shows us the TimeGenerated, Table Name, The Account, Computer Name and Activity or Operation. To avoid using more resources, please reduce the amount of results displayed to 10. 

**Use Case 2** (can be used with LogA demo workspace)

We know there's been a few users who've failed to login recently. Can you provide a deduplicated list of users who've triggered EventID 4625 over the past week?

## Extend

If you've ever done a little bit of scripting or writing commands in command prompt or powershell, you've probably heard of append. The extend operator is similar. With extend you're creating a new column within the result set and appending or attaching a string/value to it. We won't go over all the operators and functions you can use with extend, but we'll display some examples below with descriptions explaining what the query accomplished. 

**EXAMPLE 1**

![extend division](https://github.com/elmatthias33/Kusto/assets/138261985/8697ea38-bf67-47e1-a5ba-7328d8d0173d)

**EXAMPLE 2**

![extend to string](https://github.com/elmatthias33/Kusto/assets/138261985/92cc7f5a-565f-49f1-bc60-905081a4655b)

> With this query, we attached a value from under InitiatedBy back to InitiatedBy. That sounds confusing but if you were to run the query without extend, you'll find that there's already a InitiatedBy column. Without modifying that column, it's a json. It's a long string with some information that we don't want in this case. There's several ways to extract or parse that information but in this case, if you expand the InitiatedBy column, you'll see a sub field called "user". If you expand user, there's another field called UserPrinicipalName. What this query did was instead of creating a new column, we picked out a specific item to display in place of the InitiatedBy column instead of the entire string that contained some unnecessary information. 

**EXAMPLE 3**

![extend strcat](https://github.com/elmatthias33/Kusto/assets/138261985/12782b62-de31-4596-bd89-73a7bcb83b8c)

**EXAMPLE 4**

![extend case](https://github.com/elmatthias33/Kusto/assets/138261985/4d75a77f-aeef-4c49-8a4b-b4be84bc1b49)

**EXAMPLE 5**

