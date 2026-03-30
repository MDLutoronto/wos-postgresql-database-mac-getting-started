---
title: "Getting Started with the Web of Science PostgreSQL Database - Mac"
layout: "home"
description: "This tutorial will help you get up and running querying the Web of Science PostgreSQL database on a Mac computer. It will cover accessing the high performance computing environment, querying the database via SQL statements and from within a python script, and downloading the results of the query. You will need a Compute Canada account with the proper credentials to access this database. If you haven’t done so already, you should first follow the instructions to get your account set up. Note: This tutorial is intended for Mac users. If you are using Windows, check out this tutorial instead."
staff:
    - name: Kara Handren
      link: https://library.utoronto.ca/staff/kara-handren
maintainer:
 - name: Kara Handren
   link: https://library.utoronto.ca/staff/kara-handren
created_date: 2022-02-07
permalink: "/"  #! Remove this if not the homepage
---

# Getting Started with the Web of Science PostgreSQL Database - Mac

This tutorial will help you get up and running querying the [Web of Science PostgreSQL database](https://mdl.library.utoronto.ca/web-science-postgresql-database) on a Mac computer. It will cover accessing the high performance computing environment, querying the database via SQL statements and from within a python script, and downloading the results of the query.

You will need a Compute Canada account with the proper credentials to access this database. If you haven’t done so already, you should first follow the [instructions to get your account set up](https://mdl.library.utoronto.ca/technology/tutorials/how-access-web-science-postgresql-database).

Note: This tutorial is intended for Mac users. If you are using Windows, check out this [tutorial](https://mdl.library.utoronto.ca/technology/tutorials/getting-started-web-science-postgresql-database) instead.

### Table of Contents

[Access the High Performance Computing Environment](#access-the-high-performance-computing-environment)

[Query the Database via SQL](#query-the-database-via-sql)

[Download the Results](#download-the-result)

[Query the Database via Python](#query-the-database-via-python)

### Accessing the High Performance Computing Environment
{: #access-the-high-performance-computing-environment}

If working in high performance computing environment is new to you, we would recommend you attend [SciNet workshops](https://education.scinet.utoronto.ca/) to learn more, especially their Intro to SciNet & Triullium workshop (run periodically) or watch [a recording of a previous session](https://www.youtube.com/@scinethpcattheuniversityof8962).

  
But here are some steps to get your started:

1. You do not need to install any programs or clients to access the environment from a Mac. Access is via Terminal.
2. You will use an SSH key to connect. This requires some initial configuration, but once this is done it is both more secure and more convenient. If you have not already generated a key pair, instructions on how to do so can be found [here](https://mdl.library.utoronto.ca/technology/tutorials/generating-ssh-key-pairs-mac). More detailed instructions are also available on the [SciNet wiki](https://docs.computecanada.ca/wiki/Using_SSH_keys_in_Linux). Remember, you'll need create a key-pair on any systems you intend to connect with!
3. To login to the remote host, use this command in Terminal: `ssh -i .ssh/myprivatekeyname <computercanadausername>@trillium.scinet.utoronto.ca`. The system will prompt you to enter the passphrase for your key (Note, `-i .ssh/myprivatekeyname` is only necessary if you are not using the default key filepath and filename. See complete SSH setup instructions [here](https://mdl.library.utoronto.ca/technology/tutorials/generating-ssh-key-pairs-mac) for more information).
4. You are now connected to the server
5. To log out, type `exit` and press Enter. You are now back in your local environment.

### Query the Database via SQL
{: #query-the-database-via-sql}

If SQL is a new concept for you, we would first suggest you learn the basics through a tutorial, such as [this one from Tutorial Republic](https://www.tutorialrepublic.com/sql-tutorial/). You may also want to explore the [PostgreSQL documentation](https://www.postgresql.org/docs/14/index.html) to help you with your work.

1. Once logged in [as described earlier](#access-the-high-performance-computing-environment), at the prompt, type `module load postgresql` (and press Enter after this and any commands you type into the command prompt going forward)
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

### Download the Results
{: #download-the-result}

1. From the Trillium prompt, type `ls` to list all the files in your personal directory. If you followed the steps above, you should see a csv file you just saved
2. There are multiple ways to download your data file via Terminal, which are outlined in the [SciNet Wiki](https://docs.computecanada.ca/wiki/Transferring_data). If you need to ensure that two datasets remain synchronized, you may want to use rsync or Globus. Otherwise, SFTP or SCP will work well to transfer files back and forth from your local machine to the remove environment.
3. To download using SCP:
	* Open a new Terminal window that is not connected to Trillium (ie. your local directory),  and run the following command: `scp <computecanadausername>@trillium.scinet.utoronto.ca:[filename including extension] /some/local/directory`
		+ For example, to download the files to a SciNet folder in your Documents: `scp doej@trillium.scinet.utoronto.ca:myfirstqueryresult.csv /Users/user/Documents/SciNet`
		+ If prompted, enter your SSH key passphrase
		+ Note: if you received a permission denied error and are not prompted for your passphrase, try adding -i <**privatekeyname**> to the scp command: `scp -i <privatekeyname> <computecanadausername>@trillium.scinet.utoronto.ca:[filename including extension] /some/local/directory`
4. You should see a progress bar, which will show 100% once the download is complete
5. Now if you go to that directory, you should see your new csv file. Open it up and view your results!

### Query the Database via Python
{: #query-the-database-via-python}

If you would like to programmatically construct your SQL statements (and programmatically manipulate the results), you may prefer to use Python code to query the database.

If Python is new for you, we would first suggest you learn the basics through a tutorial, such as [this one from W3Schools](https://www.w3schools.com/python/default.asp). You can also consult our recorded workshop [A Friendly Introduction to Python for Absolute Beginners: Part 1](http://play.library.utoronto.ca/watch/d17a5f60462dec00565b7809d2953757), as well as the [Setup Instructions](https://maps.library.utoronto.ca/workshops/PythonPart1/SetupInstructions.pdf) (includes how to get slides, workshop files, etc.) & [Solutions](https://maps.library.utoronto.ca/workshops/PythonPart1/WorkshopSolutions.zip) (packaged in a zip file) for this workshop.

1. In your favourite Python editor, write your script and save your file as a .py file. For this example, we will call it **myfirstpythonscript.py**. Here is an example of a Python script that takes a list of author names and finds their publications ([download the script](https://mdl.library.utoronto.ca/sites/default/public/mdldata/open/international/wos/myfirstpythonscript.py)- note that you may have to right click to save the Python script instead of viewing the text in a browser tab). This script creates a temporary table of our authors, and then joins that table to the author table to find the authors’ publication IDs (this is more efficient than calling multiple SELECT statements in a loop, one for each author). Then this table is joined with the publication table to find the publication titles. You will see that this script uses the [psycopg2](https://www.psycopg.org/docs/index.html) package and provides information on how to connect to the database. You do not have to specify a username and password, as the system will automatically detect if you have permission. You can use this script and the SQL statement examples above, as guides to create your own Python code to query the database:

    ```
    # You need a couple of packages to query the database and write a CSV file
    import psycopg2
    import csv

    # You will need this database name and host information to create a connection to the database
    database_config = {
        'dbname': 'wos',
        'host': 'idb1'
    }

    # This is a list of names we are searching for. Feel free to edit the names to find publications 
    # from researchers you are interested in
    author_names = [
        'Dearborn, Dylanne',
        'Fortin, Marcel',
        'Handren, Kara',
        'Schultz, Michelle Kelly',
        'Trimble, Leanne',
    ]

    # This section of code uses the psycopg2 package to connect to the database
    con = psycopg2.connect(**database_config)
    cur = con.cursor()

    # This executes a SQL statement that creates a temporary table with our list of author names 
    cur.execute('CREATE TEMPORARY TABLE _author (name TEXT)')
    for name in author_names:
        cur.execute('INSERT INTO _author VALUES (%s)', (name,))

    # This SQL statement joins our list of names with the database author table to filter the results
    # to only the authors we are looking for. This is a more efficient approach than looping through
    # author names and running multiple SELECT statements
    # Note: Using a backslash as the line is long - not part of the SQL statement
    cur.execute("SELECT wos_id, full_name FROM author INNER JOIN _author ON author.full_name \
    ILIKE '%'||_author.name||'%'")

    # This next section of code goes line by line through the results and adds them to a dictionary 
    # data type in python, where the publication id for the author is the key and the name of the
    # author is the value. It also prints it out so you can see the data.
    mylist = dict()
    while result := cur.fetchone():
        print(result)
        mylist[result[0]] = result[1]

    # This next section sets up a CSV that we will use to store the results of our final query
    with open('myfirstpythonresults.csv', mode='w', encoding='UTF8', newline='') as csv_file:
        myheader = ['title']
        writer = csv.writer(csv_file)
        writer.writerow(myheader)

        # This section goes through each item in the dictionary that we created earlier. For each
        # key (which is an author’s publication ID), it queries the database to find its title. Then
        # it writes that title in the CSV file. It also prints it out so you can see the data.
        for x in mylist.keys():
            cur.execute("SELECT title FROM publication WHERE publication.id=%s", (x,))
            while finalresult := cur.fetchone():
                print(finalresult)
                writer.writerow(finalresult)

    # Finally, all the connections to the database are closed
    cur.close()
    con.close()
    ```
2. Once your Python script is ready, use SCP to to upload from your local computer. This is the same for downloading ([as described earlier](#download-the-result)), but the order of directories is reversed: `scp /your/local/directory/:[filename and extension] <computecanadausername>@trillium.scinet.utoronto.ca:/home/[firstinitialofyourlastname]/<computecanadausername>/<computecanadausername>.` Note: If you are not the Principal Investigator ie. your account was sponsored by another user, you'll need to substitute that person's username in place of the first <**computecanadausername**>, as well as their first initial in [firstinitialofyourlastname]. In this case: `scp /your/local/directory/:[filename and extension] <computecanadausername>@trillium.scinet.utoronto.ca:/home/[firstinitialofyoursponsorslastname]/<sponsorscomputecanadausername>/<computecanadausername>`
	* For example: `scp /Users/user/Documents/SciNet/myfirstpythonscript.py doej@trillium.scinet.utoronto.ca:/home/d/doej/doej`
	* For example, for a sponsored account (smithp sponsored by doej): `scp /Users/user/Documents/SciNet/myfirstpythonscript.py smithp@trillium.scinet.utoronto.ca:/home/d/doej/smithp`
	* If prompted, enter your SSH key passphrase
3. Once your script has been uploaded, connect to Trillium [as described earlier](#access-the-high-performance-computing-environment)
4. Next we need to set up the environment to run our Python script. Type `module load python/3.9.8`
5. Next type `virtualenv --system-site-packages myenv`
6. Next type `source myenv/bin/activate`
7. Finally type `pip install psycopg2-binary`
8. Once the package has installed, you are ready to run your Python script. Type `python myfirstpythonscript.py` or substitute in the name of your Python script if you called it something else.  
(Important Note: If querying is only a small part of the overall task, and the majority of computing effort is going into postprocessing the query results, for example, using natural language processing or graph analysis, to be done in parallel, then there are different ways to run your script that involve [submitting it as a job](https://docs.scinet.utoronto.ca/index.php/Niagara_Quickstart#Submitting_jobs) to be run. Feel free to [contact us](https://mdl.library.utoronto.ca/about/contact-form) for help.)
9. It may take a while to run, but when it is finished you will see the command prompt again, and now if you type `ls`from the Trillium prompt, you should see a new CSV file created from the Python script. Download the file ([as described earlier](#download-the-result)) and open up the file to see the results

These are just a few examples to help you get started, but of course there is much more you can do. If you have any questions, feel free to [contact us](https://mdl.library.utoronto.ca/about/contact-form).
