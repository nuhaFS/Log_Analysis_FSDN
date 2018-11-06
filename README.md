# Log Analysis

#### Log analysis is a simple script to summarize data extracted from the News Blog database. Using this script will return an analytical report for each of the following questions:

* What are the most popular three articles of all time?
* Who are the most popular article authors of all time?
* On which days did more than 1% of requests lead to errors?

### About the database
The database contains three tables:
* articles - contains ( author, title, slug, lead, body, time, id)
* authors - (name, bio, id)
* log - (path, ip, method, status, time, id)

**To connct to the database use**
```
psycopg2.connect(database=DB)
```

## What You Need
To setup your virtual machine and download required database file, please Check the "Prepare the software and data" section in the project details on Udacity.

To run this script you will need to install **Psycopg2** and **Tabulate**

* If you are using **Python2** run the following code in your Command Line

```
pip install psycopg2
```

and

```
pip install tabulate
```

* If you are using **Python3** run the following in your Command Line

```
pip3 install psycopg2
```

and

```
pip3 install tabulate
```
### Wheel Packages Error
When running the script via Python3, although it returns results, it also show this error first:
**"144: UserWarning: The psycopg2 wheel package will be renamed from release 2.8; in order to keep installing from binary please use "pip install psycopg2-binary" instead."**

#### If the provided command did not work, try to use this one:
```
pip3 install --pre -i https://testpypi.python.org/simple psycopg2-binary
```
Source: [Solving the problems with wheel packages](https://www.postgresql.org/message-id/CA%2Bmi_8bd6kJHLTGkuyHSnqcgDrJ1uHgQWvXCKQFD3tPQBUa2Bw%40mail.gmail.com). For details see:[Binary install from PyPI](http://initd.org/psycopg/docs/install.html#binary-install-from-pypi)

## Database Views:
The following are the view queries you will need to set up your database:

### Most Viewed Articles
This view will serve as a base query. It returns the articles according to most viewed ones.

> CREATE VIEW MostViewedArticles as SELECT articles.slug, COUNT(log.path) as NumberOfViews FROM log, articles WHERE SUBSTRING( log.path, 10) = articles.slug GROUP BY articles.slug ORDER BY NumberOfViews DESC;


### Detailed Status
This view generates the results for the logs status in details, showing the count of successful and failed  requests according to date.

> CREATE VIEW DetailedStatus as  SELECT COUNT(CASE WHEN status='200 OK' THEN 1 ELSE NULL END) as OkStatus, COUNT(CASE WHEN status= '404 NOT FOUND' THEN 1 ELSE NULL end) as NFStatus, date(time) as date FROM log GROUP BY date;


### Status Percentages
This view generates the Percentages for logs status per each day.

> CREATE VIEW PercentageOfStatus as SELECT (okstatus/ SUM( okstatus + nfstatus) * 100 ) as Ok, (nfstatus/ SUM( okstatus + nfstatus) * 100) as Error, date FROM DetailedStatus GROUP BY date, okstatus, nfstatus;
