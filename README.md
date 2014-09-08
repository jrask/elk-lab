#elk-lab

Logstash, Elasticsearch and kibana lab for Jayway devops team


##Preparations
ElasticSearch and kibana comes embedded with logstash so that is good enough for this lab.


##Test setup

**File inputs need absolute paths, open logstash/logstash.conf and edit paths to input files.**

Goto elk-lab directory and start with:
 
    logstash agent -f logstash/logstash.conf --debug -- web

Open a browser and goto http://localhost:9292/index.html. Somewhere in the text on the kibana default page is a link to a default logstash dashboard, click that link.

You should now see an empty dashboard, it might say that there are no indices in the specified time period which is fine since we have not stored anything.

in elk-lab directory, write:

    echo 'Hello world' >> logs/access.log
	
Refresh dashboard and you should have a green line in the histogram and an event below. 

Ready to go!

## Lab

Unless you have any log files by your self you will get some handed to you before the lab.

1. Copy the file(s) into logs and name it access.log, this will cause logstash to read the entire file into elasticsearch.
2. Check the dashboard, what do you see? what can we do now, can you think of something to graph?
   Basically, the only thing we can do now is to search the files not much more.
   
3. Next we need to extract parts of the file into key value pairs. I.e IP address, statuscodes, url, response time etc
   In logstash, this is done with grok patterns. 
   Hint: Combined apache log + response time
   
   We also need to extract the real time from the logs, the current time used is when it was read from the file.
   Hint: date filter
   
   WIP!
      Open a browser and goto http://localhost:8080. Somewhere in the text on the kibana default page is a link to a default logstash dashboard, click that link.
    * Grok message (hint combined apache log) + response time
    
## Extra Labs

### Collectd
Collectd (https://collectd.org/) can be use to collect various stats from a host and store or forward that to a central monitoring point. Logstash comes with built-in support for collected in the collectd input plugin.

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

The collectd-tg tool (http://collectd.org/documentation/manpages/collectd-tg.1.shtml) can be used to generate load data.

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
       
