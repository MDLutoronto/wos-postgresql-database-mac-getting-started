---
title: Query the Database via SQL
parent: Getting Started with the Web of Science PostgreSQL Database - Mac
layout: default
nav_order: 2
staff:
    - name: Kara Handren
      link: https://library.utoronto.ca/staff/kara-handren
maintainer:
    - name: Leslie Barnes
      link: https://library.utoronto.ca/staff/leslie-barnes
created_date: 2022-02-07
---

## Query the Database via SQL

If SQL is a new concept for you, we would first suggest you learn the basics through a tutorial, such as [this one from Tutorial Republic](https://www.tutorialrepublic.com/sql-tutorial/). You may also want to explore the [PostgreSQL documentation](https://www.postgresql.org/docs/14/index.html) to help you with your work.

1. Once logged in [as described earlier](https://mdlutoronto.github.io/wos-postgresql-database-mac-getting-started/1-access-the-high-performance-computing-environment/), at the prompt, type `module load postgresql` (and press Enter after this and any commands you type into the command prompt going forward)
2. Type `psql -h idb1 -d wos` to start up the command-line interface to PostgreSQL and be able to query the Web of Science database
3. Type `\?` for help with psql commands (whenever you see “—More—” at the bottom of the screen, press the space bar to page through the information)
4. Let’s try a few of these psql commands. Type `\x` to have a nicer expanded display of the outputs/results
5. Type `\dt` to see a list of all the tables available to you. To learn more about these tables, you can also consult [the documentation](https://mdl.library.utoronto.ca/sites/default/public/mdldata/open/international/wos/db-structure.pdf)
6. You can type \d <**tablename**> to display columns for a particular table. Type `\d publication` to see all the columns for the publication table, for example
7. Once you have a better understanding of the database’s organization into tables, you can type SQL statements ending with a semi-colon to query the database and see the results. Remember, you can page through results with the spacebar
8. Before you begin, you might want to type `\h` for help with SQL commands. This will list all of the SQL commands available. If you want to learn more about a particular command, such as SELECT, type `\h select`
9. Let’s try out a few SQL examples relevant to this particular database. If you want to paste these long statements into Terminal, copy the code and then go to the Terminal and right click and select paste, or press COMMAND + V. However, you might want to type the statements out to examine them more closely. Some of the examples may take a few moments to run, so be patient. (***Note**: If you would like to limit your results to a random sampling (this will speed things up and is great for testing!) the following command can be added to the end of the examples below:* `order by random() limit 10).` You will know when it is done, as you will see results on the screen or the command prompt will reappear. If you want to re-run a command, a quick way to “re-type” it is to use the up and down arrow keys to cycle through previously entered commands. If the results list seems too long to page through every item, type `q` to stop showing the results:

    1. **Search by Title:** 
    Let’s find publications that have the words “visualization”, and “library” OR “libraries” OR “librarian” in the title. (The % symbol is a wildcard symbol in SQL when using the ILIKE operator.) Type
    ```
    SELECT * 
    FROM publication 
    WHERE publication.title ILIKE '%visualization%' 
    AND publication.title ILIKE '%librar%';
    ```
    2. **Search by Title words and Year:** 
    We can also limit searches based on multiple criteria for different fields. Let’s run the same search as above, but limit it to only publications published later than 2015. Type
    ```
    SELECT * 
    FROM publication 
    WHERE publication.title ILIKE '%visualization%' 
    AND publication.title ILIKE '%librar%' 
    AND publication.year > 2015;
    ```
    3. **Search by Title words and Year, return specific fields only:** 
    We have been selecting all the fields in the publication table, but we can instead only pick the ones of interest. Let’s only output the publication title and year. Type
    ```
    SELECT publication.title, publication.year 
    FROM publication 
    WHERE publication.title ILIKE '%visualization%' 
    AND publication.title ILIKE '%librar%' 
    AND publication.year > 2015;
    ```
    4. **Search by Title words and Year, but return Author information as well:** 
    So far these queries have focused on returning data from one table, but you can join tables to get information from multiple tables, such as publication and author. Let’s run the same search from above, but also get author names included in the results. Type
    ```
    SELECT * 
    FROM publication 
    INNER JOIN author ON publication.id=author.wos_id 
    WHERE publication.title ILIKE '%visualization%' 
    AND publication.title ILIKE '%librar%' 
    AND publication.year > 2015; 
    ```
    (Note: This will result in publication titles being duplicated if there are multiple authors to list)
    5. **Search by Title words and Year, but return Author and Source information as well:** 
    You can join one table to more than one other table to pull in more information into your results. Let’s run the query from example d, but add the journal information as well. Type
    ```
    SELECT * 
    FROM publication 
    INNER JOIN author ON publication.id=author.wos_id 
    INNER JOIN source ON publication.source_id=source.id 
    WHERE publication.title ILIKE '%visualization%' 
    AND publication.title ILIKE '%librar%' 
    AND publication.year > 2015;
    ```
    6. **Search by Title words, Year and Author name:** 
    You can also limit searches based on information in these multiple tables. Let’s run the same search from above, but also limit to only authors with the last name “Reid”. Type
    ```
    SELECT * 
    FROM publication 
    INNER JOIN author ON publication.id=author.wos_id 
    INNER JOIN source ON publication.source_id=source.id 
    WHERE publication.title ILIKE '%visualization%' 
    AND publication.title ILIKE '%librar%' 
    AND publication.year > 2015 
    AND author.full_name ILIKE 'Reid, %';
    ```
    7. **Search by Year and Source name:** 
    This can continue to get more complicated. You might want to join a table in order to query a field, but aren’t interested in including the data from that table in the final results. The next example illustrates this and introduces you to nested SELECT statements (which will come in handy later). Often these SQL statements are best deciphered reading from the inside out (i.e., reading what is in the innermost parentheses first and working outwards) and breaking the statement into distinct parts. Note you could also construct this query similar to example f above, the only difference is that the columns from the source table are not included here, but are included in example f. For this example, let’s query the database to find all recent publications with author information for publications from the journal called “Scientometrics”. Working inside out, this query finds all the source IDs where the source name is “Scientometrics” then filters publications that have that source ID, plus the other criteria outlined below. Type
    ```
    SELECT * 
    FROM publication 
    INNER JOIN author ON publication.id = author.wos_id 
    WHERE publication.year > 2019 AND publication.id IN 
    (SELECT publication.id 
    FROM publication 
    INNER JOIN source ON publication.source_id = source.id 
    WHERE source.name ILIKE 'Scientometrics'); 
    ```
    (Note: Using ILIKE for the source name makes it case insensitive when searching)
    8. **Search by Title words, Year and Author institution:** 
    Some tables in the database are bridging tables, where there are many-to-one relationships, such as an author having many addresses. Here’s another example where you work backwards using nested SELECT statements. Let’s query the database to find all publications with the word “visualization” in the title, published in the last couple of years from authors from the University of Toronto. First you find all the address IDs that are for the University of Toronto, then you find all the author IDs that have those address IDs, and then filter by those authors, plus the other criteria outlined below. (Note: Just to simplify the query and make it run faster for this example, we're just looking for addresses with "Univ Toronto". If we changed it to "Univ%Toronto" we would find more, but it would also take more time.) Type
    ```
    SELECT publication.title, author.full_name 
    FROM publication 
    INNER JOIN author ON publication.id = author.wos_id 
    WHERE publication.title ILIKE '%visualization%' 
    AND publication.year > 2019 
    AND author.id IN 
    (SELECT author.id 
    FROM author 
    INNER JOIN author_address ON author.id = author_address.author_id 
    WHERE author_address.address_id IN 
    (SELECT address.id 
    FROM address 
    WHERE address.address ILIKE '%Univ Toronto%')); 
    ```
    (Note: This query might take a bit of time to run!)
    9. **Search by Keywords and Year:** 
    Here we are using another bridging table, this time to find publications based on a particular descriptor, such as a subject or keyword. More details about this table can be found in [the documentation](https://mdl.library.utoronto.ca/sites/default/public/mdldata/open/international/wos/db-structure.pdf). This example is similar to the one above except searching by [Keywords Plus](https://images.webofknowledge.com/images/help/WOS/hp_full_record.html#:~:text=KeyWords%20Plus%C2%AE%20are%20index,traditional%20keyword%20or%20title%20retrieval.) (standardized keywords in the Web of Science dataset) instead of author affiliation. Let’s query the database to find all publications from 2020 that have a Keywords Plus field roughly equal to “Artificial Intelligence”. First you find all the descriptor IDs that have Keywords Plus fields with the text roughly matching “Artificial Intelligence”, then you find all the publication IDs that have those descriptor IDs, and then filter by those publications IDs, plus only take publications from 2020. Type
    ```
    SELECT * 
    FROM publication 
    WHERE publication.year = 2020 AND publication.id IN 
    (SELECT publication.id 
    FROM publication 
    INNER JOIN publication_descriptor ON publication.id = publication_descriptor.wos_id
    WHERE publication_descriptor.desc_id IN 
    (SELECT descriptor.id 
    FROM descriptor 
    WHERE descriptor.text ILIKE 'Artificial%Intelligence' 
    AND descriptor.type='kw_plus')); 
    ```
    (Note: This query WILL take a bit of time to run! Good time for a coffee break!)
    10. **Search by Title words and Year, returning only publication title and abstract:** 
    One useful field for text analysis that we haven't seen in our examples yet would be to obtain abstracts for the items found. Let’s run a search with similar search parameters to example b, but return titles and abstracts only. Type
    ```
    SELECT publication.title, abstract.text 
    FROM publication 
    INNER JOIN abstract ON publication.id=abstract.wos_id 
    WHERE publication.title ILIKE '%visualization%' 
    AND publication.title ILIKE '%librar%' 
    AND publication.year > 2019; 
    ```
    11. **Search for articles that cite a subset of articles:** 
    The Web of Science dataset is very valuable to analyze citation networks. For example, we can use another bridging table called references to find all publication IDs that cited or are cited by other publication IDs. Let’s query the database to find all the articles that cite a (very small) subset of items. The subset is similar to example b above, find all articles that have the words “visualization”, and “library” OR “libraries” OR “librarian” in the title, but this time only published after 2019. These types of queries are intensive and can take a while to run, so this is a very simple and small example to get you started. Type
    ```
    SELECT publication.title 
    FROM publication 
    WHERE publication.id IN
    (SELECT reference.citing_id
    FROM reference
    INNER JOIN publication ON reference.cited_id = publication.id
    WHERE publication.title ILIKE '%visualization%' 
    AND publication.title ILIKE '%librar%' 
    AND publication.year > 2019);
    ```
    (Note: This query WILL take a bit of time to run! )
    12. **Search for articles that are cited by a subset of articles:** 
    We can also query this the opposite way to find articles cited by a subset of articles. Let’s query the database to find all the articles that are cited by a (very small) subset of items. The subset is the same as in example k, and the modifications to the query in example k are minimal. Type
    ```
    SELECT publication.title 
    FROM publication 
    WHERE publication.id IN
    (SELECT reference.cited_id
    FROM reference
    INNER JOIN publication ON reference.citing_id = publication.id
    WHERE publication.title ILIKE '%visualization%' 
    AND publication.title ILIKE '%librar%' 
    AND publication.year > 2019);
    ```
    (Note: This query WILL take a bit of time to run!)  
    You can also combine ideas from examples k and l to start building your own full citation networks that go in both directions, citing and cited by.
10. After running some test SQL statements, if you are happy with the statement, instead of displaying the results on the screen, you can save them to a CSV file instead. If you want to save the results of our first SQL statement example above to a file called **myfirstqueryresult.csv**, type

    ```
    \copy (SELECT * 
    FROM publication 
    WHERE publication.title ILIKE '%visualization%' 
    AND publication.title ILIKE '%librar%') 
    TO 'myfirstqueryresult.csv' CSV HEADER;
    ```
    (Notes: You don’t need a semi-colon after the SQL statement in this case, but at the end of the copy command instead. Also, make sure that there is a space between \copy and the open parenthesis. When the command is finished, you will see the word COPY, followed by the number of results.)
11. When you are finished querying the database and saving your results, type `\q` to quit the psql program

Throughout the examples, we have been using plain wildcard pattern matching, but you may want to explore [more sophisticated ways to search text](https://www.postgresql.org/docs/14/textsearch.html) as well.

A Note on Query Efficiency: Generally, Postgres is really smart at analyzing what you want to do and querying the database in the most efficient way, so often changing the query structure won't make any difference because Postgres really does the same thing under the hood. One thing that sometimes helps is to increase the number of workers, for example with this command `SET max_parallel_workers_per_gather = 16`, but not all operations in a query can be parallelized or parallelized well. Another thing that potentially helps for complex queries is to use temporary tables instead of table variables. For example, [rewriting example h](http://mdl.library.utoronto.ca/sites/default/public/mdldata/open/international/wos/rewritten_example_h.txt) to use temporary tables sped up the query from minutes to seconds.

**Technique:** [Searching for maps and data](https://mdlutoronto.github.io/tutorials-search/?technique=Searching+for+maps+and+data), [Text and Data Mining](https://mdlutoronto.github.io/tutorials-search/?technique=Text+and+Data+Mining) \| **Tools:** [Web of Science](https://mdlutoronto.github.io/tutorials-search/?tool=Web+of+Science)