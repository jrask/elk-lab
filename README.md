#elk-lab

Logstash, Elasticsearch and kibana lab for Jayway devops team


##Preparations
Install logstash (http://logstash.net/), homebrew works fine (brew install logstash)
Install Kibana (https://github.com/elasticsearch/kibana)
 - Kibana are pure html files so any webserver will work. Easiest way is to use ‘python -m SimpleHTTPServer 8080’
If your are using git clone you should use kibana/src as your web root

ElasticSearch comes embedded with logstash so that is good enough for this lab.


##Test setup

**File inputs need absolute paths, open logstash/logstash.conf and edit paths to input files.**

Goto elk-lab directory and start with 
    logstash agent -f logstash/logstash.conf --debug

Goto kibana/src directory and start kibana with (i.e)
    python -m SimpleHTTPServer 8080

Open a browser and goto http://localhost:8080. Somewhere in the text on the kibana default page is a link to a default logstash dashboard, click that link.

You should now see an empty dashboard, it might say that there are no indices in the specified time period which is fine since we have not stored anything.

in elk-lab directory, write 
    echo 'Hello world' >> logs/access.log
	
Refresh dashboard and you should have a green line in the histogram and an event below. 

Ready to go!

## Lab

WIP!