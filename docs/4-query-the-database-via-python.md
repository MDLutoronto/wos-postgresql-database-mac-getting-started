---
title: Query the Database via Python
parent: Getting Started with the Web of Science PostgreSQL Database - Mac
layout: default
nav_order: 4
---

### Query the Database via Python

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