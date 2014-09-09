#elk-lab

Logstash, Elasticsearch and kibana lab for Jayway devops team


##Preparations
ElasticSearch and kibana comes embedded with logstash so that is good enough for this lab.


##Test setup

**File inputs need absolute paths, open logstash/logstash.conf and edit paths to input files.**

Goto elk-lab directory and start with:
 
    logstash agent -f logstash/logstash.conf --debug -- web

Open a browser and goto http://localhost:9292/index.html#/dashboard/file/logstash.json.

In kibana, in top right corner, click configure dashboard -> index -> and rename index pattern from [logstash-]YYYY.MM.DD to [logstash-nginx-]YYYY.MM.DD.
You should now see an empty dashboard, it might say that there are no indices in the specified time period which is fine since we have not stored anything.

in elk-lab directory, write:

    echo 'Hello world' >> logs/access.log
	
Refresh dashboard and you should have a green line in the histogram and an event below. 

Ready to go!

## Lab

1. Copy the file(s) into logs and name it access.log, this will cause logstash to read the entire file into elasticsearch.
Unless you have any log files by your self you will get some handed to you before the lab.

Check the dashboard, what do you see? what can we do now, can you think of something to graph?
Basically, the only thing we can do now is to search the files not much more.

Delete the index! curl -XDELETE http://localhost:9200/logstash-nginx-2014.09.08
   
* Lab 1. Next we need to extract parts of the file into key value pairs. I.e IP address, statuscodes, url, response time etc and also make sure that the correct date is used

   In logstash when to parse and extract, this is done with grok patterns, so checkout the grok filter.
   There is an excellent app for testing patterns that have support for all patterns that logstash has, https://grokdebug.herokuapp.com/
   
   Hint: Combined apache log + response time
   
   We also need to extract the real time from the logs, or go the simple way and change time field in kibana
   Hint: date filter
   
   You have successfully completed this lab when new fields like response, verb, referrer etc shows up on the left side of your event panel AND
   all 'events' are correctly drawn in the histogram using correct timestamp, not crunch time.
   
* Lab 2. Lets draw some graphs
   
   : Histogram över response times, 0-20ms, 21-100ms, 101-200ms, 201-1sec, 1.001 - *
   : Pie chart showing status codes with count
       
## Extra Labs

### Collectd
[Collectd](https://collectd.org/) can be use to collect various stats from a host and store or forward that to a central monitoring point. Logstash comes with built-in support for collectd in the [collectd input plugin](http://logstash.net/docs/1.4.2/inputs/collectd).

configure logstash to listen to collectd:

    input {
       udp {
           host => "127.0.0.1"
           port => 25826
           buffer_size => 1452
           codec => collectd { }
           type => "collectd"
       }
    }

install collectd:

    brew install collectd      

configure collectd:

    edit collectd/collectd.conf
    
start collectd in foreground:

    /usr/local/Cellar/collectd/5.4.1/sbin/collectd -C collectd/collectd.conf -f
   
Collectd will by default report every 10th seconds. Now open the kibana UI and very that logstash events gets reported.

The [collectd-tg] (http://collectd.org/documentation/manpages/collectd-tg.1.shtml) tool can be used to generate load data.

    collectd-tg -n 10000 -H 2 -i 10 -d 127.0.0.1 -D 25826
  
* Lab1: Add a histogram shown disk free
* Lab2: Add a histogram over load
* Lab3: Add a histogram over incomming (rx) and outgoing (tx) network traffic

### Running kibana separately
   Install Kibana (https://github.com/elasticsearch/kibana)
    - Kibana are pure html files so any webserver will work. Easiest way is to use ‘python -m SimpleHTTPServer 8080’
   If your are using git clone you should use kibana/src as your web root
   
   Goto kibana/src directory and start kibana with (i.e)
       python -m SimpleHTTPServer 8080
       
