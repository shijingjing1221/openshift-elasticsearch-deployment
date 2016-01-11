# openshift-elasticsearch-deployment
Base the blog https://blog.openshift.com/searching-with-elasticsearch-on-openshift/, and do some improvement with elasticsearch 2.1


<h2>Create a new Openshift DIY app</h2>
<ol>
<li>Create a new app with type <strong>Do-It-Yourself 0.1</strong>, I'm creating it with name elastic, and the git url is
<pre><code>ssh://XXX@elastic-shijingjing1221.rhcloud.com/~/git/elastic.git/</code></pre>
</li>
<li>
Connect the app with ssh
<pre><code>ssh XXX@elastic-shijingjing1221.rhcloud.com
</code></pre>
</li>
</ol>

<h2>Download the elasticsearch package</h2>
<ol>
<li>Re-locate to the data folder
<pre><code>cd $OPENSHIFT_DATA_DIR</code></pre>
</li>
<li>
Download the elasticsearch with version 2.1.1
<pre><code>wget https://download.elasticsearch.org/elasticsearch/release/org/elasticsearch/distribution/tar/elasticsearch/2.1.1/elasticsearch-2.1.1.tar.gz
</code></pre>
</li>
<li>
Rename elasticsearch
<pre><code>tar xf elasticsearch-2.1.1.tar.gz
</code><code>mv elasticsearch-2.1.1 elasticsearch
</code></pre>
</li>
</ol>


<h2>Edit the elasticsearch conf file to fit the openshift</h2>
<ol>
<li>
<pre><code>vim elasticsearch/config/elasticsearch.yml
</code></pre>
Add the following lines to the elasticsearch.yml
<pre><code>network.host: ${OPENSHIFT_DIY_IP}
transport.tcp.port: 3306
http.port: ${OPENSHIFT_DIY_PORT}
</code></pre>
</li>
<li>
Stop the default ruby service by running the following cmd
<pre><code>ctl_app stop</code></pre>
</li>
<li>
Now you can start the elastcsearch by following command, and test whether it can be properly run on openshift
<pre><code>./elasticsearch/bin/elasticsearch
</code></pre>
Open the link to test in browser http://elastic-shijingjing1221.rhcloud.com/ <br/>
Enter `Ctrl+C` to exit
</li>
</ol>

<h2>Start the elasticsearch as a service</h2>
<ol>
<li>
Checkout the code from git
<pre><code>git checkout ssh://XXX@elastic-shijingjing1221.rhcloud.com/~/git/elastic.git/
</code></pre>
</li>
<li>
Edit the file <i>.openshift/action_hooks/start</i>, replace the content to 
<pre><code>${OPENSHIFT_DATA_DIR}elasticsearch/bin/elasticsearch -d
</code></pre>
Edit the file <i>.openshift/action_hooks/stop</i>, replace the content to 
<pre><code>
#!/bin/bash
source $OPENSHIFT_CARTRIDGE_SDK_BASH

# The logic to stop your application should be put in this script.
# if [ -z "$(ps -ef | grep testrubyserver.rb | grep -v grep)" ]
if [ -z "$(ps -ef | grep elasticsearch | grep -v grep)" ]
then
    client_result "Application is already stopped"
else
    # kill `ps -ef | grep testrubyserver.rb | grep -v grep | awk '{ print $2 }'` > /dev/null 2>&1
      kill `ps -ef | grep elasticsearch | grep -v grep | awk '{ print $2 }'` > /dev/null 2>&1
  
fi
</code></pre>
</li>
<li>
Push the code, and then the elastcsearch is running as a service.
<pre><code>git push
</code></pre>
</li>
</ol>

