SQL Injection is a common vulnerability even in modern systems. 

The mother of all resources on this attack can be found here: https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection

I will not be able to top this resource nor effectively summarize all the valid SQLi payloads. Refer to this manual instead.

This site has additional resources, including a SQLi backdoor technique: [SQL Injection & XSS Playground | Red Team Notes](https://www.ired.team/offensive-security-experiments/offensive-security-cheetsheets/sql-injection-xss-playground)

See [[Sqlmap]] for command details and cheatsheet. 
See [[Wafwoof]] for WAF detection and PayloadsAllTheThings for WAF bypasses. 
See [[Burp Suite]] for manual injections with easier replayability. 

# UNION-Based

After confirming an SQLi exists, you may use the UNION keyword to return results from several select statements. 
1. Confirm the number of columns. Add columns to your UNION statement until they match
	- `UNION 1`
	- `UNION 1,2`
	- `UNION 1,2,3`
	- `UNION 1,2,3,4`
2. Identify database information. Use `database()` and `group_concat()` to display information about the database you have access to.
	- `UNION 1,2,3,database()`
	- `UNION 1,2,3,group_concat(table_name) from information_schema.tables where table_schema = '<database name>'`
	- `UNION 1,2,3,group_concat(column_name) from information_schema.columns where table_name = '<table name>'`
3. Get the data you want from the database
	- `UNION 1,2,3,group_concat(username,':',password SEPARATOR '<br>') from <table name>`

# BLIND SQLi

## Authentication Bypasses
The cheat sheet above has a large list of auth bypasses for SQLi-vulnerable login forms. Select the correct one of these and proceed accordingly. 

## Boolean Based
Boolean-based SQLi occurs when a value is returned to the user when a query evaluates true. For instance, the function that determines if a user exists or not. Using this method, we can evaluate whether or not users exist, what table names are, etc using the SQL wildcard operator '%'. 

## Time Based
Same as Boolean-Based except using a SQL `sleep(?d)` statement to induce a delay on a success. For this attack , your query should evaluate to false by default. 
