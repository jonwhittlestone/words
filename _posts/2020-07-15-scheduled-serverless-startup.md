---
keywords: fastai
description: Using AWS lambda to automate boot up and down of a Digital Ocean droplet.
title: Scheduled Severless Startup
toc: true 
badges: true
comments: true
categories: [AWS Lambda, automation, python]
image: http://i.imgur.com/dIAyXJa.png
show_image: true
hide: true
nb_path: _notebooks/2020-07-15-scheduled-serverless-startup.ipynb
layout: notebook
---

<!--
#################################################
### THIS FILE WAS AUTOGENERATED! DO NOT EDIT! ###
#################################################
# file to edit: _notebooks/2020-07-15-scheduled-serverless-startup.ipynb
-->

<div class="container" id="notebook-container">
        
    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">

</div>
    {% endraw %}

<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h1 id="The-Problem">The Problem<a class="anchor-link" href="#The-Problem"> </a></h1><p>As part of the early Covid-19 response, Digital Ocean donated some free credit to us to host an app for a local food delivery scheme.</p>
<p>To make that credit go as far as possible and to minimise power consumption, we'd like to power up and down the servers according to a schedule.</p>
<h1 id="The-Solution">The Solution<a class="anchor-link" href="#The-Solution"> </a></h1><p>Here's how to do that with AWS Lambda, with cloudfront events. We iterate on that to use the Serverless Framework.</p>
<p><strong>TLDR; Have a look at the <a href="https://github.com/jonwhittlestone/scheduled-serverless-startup">companion repo.</a>. It contains an example dockerized web app and the shell scripts for starting containers and creating the service.</strong></p>
<p>This article covers how to automate with a Digital Ocean droplet so to follow along with the code, you can create one using the example from <a href="http://words.howapped.com/systemd/automation/2020/06/01/automatically-restart-docker-after-reboot-with-service.html#cURL-to-create-the-droplet">a previous article</a>.</p>
<p>We will examine the cURL statements, convert those into Python. We can use AWS Lambda to execute the Python in a serverless environment. Then, the AWS lambda functions can be triggered by Cloudwatch events to a schedule so machines can be brought online only during operating hours.</p>
<p>We can notify any interested parties using Microsoft Teams - a topic for a subsequent post.</p>
<h2 id="Prerequisites">Prerequisites<a class="anchor-link" href="#Prerequisites"> </a></h2><p>You can always check the <a href="https://github.com/jonwhittlestone/scheduled-serverless-startup">companion repo.</a></p>
<p>You will need to:</p>
<ul>
<li>.. have <code>jq</code> installed to format JSON responses<ul>
<li><a href="https://stedolan.github.io/jq/download/">Download JQ</a> for your OS</li>
</ul>
</li>
</ul>
<ul>
<li>.. have a server on which a dockerized web app will start automatically when the server is restarted<ul>
<li>See previous article on how to <a href="http://words.howapped.com/systemd/automation/2020/06/01/automatically-restart-docker-after-reboot-with-service.html#cURL-to-create-the-droplet">create one with DigitalOcean</a>.</li>
</ul>
</li>
</ul>
<ul>
<li>.. have <a href="https://boto3.amazonaws.com/v1/documentation/api/latest/guide/quickstart.html#installation">installed Boto3 with your AWS account credentials</a> so we can work with the AWS Python SDK.</li>
</ul>

</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h2 id="cURL-statements-to-boot-down-and-up">cURL statements to boot down and up<a class="anchor-link" href="#cURL-statements-to-boot-down-and-up"> </a></h2><p>As covered in a <a href="http://words.howapped.com/systemd/automation/2020/06/01/automatically-restart-docker-after-reboot-with-service.html">previous post</a>, our web app will restart when rebooted using a systemd service.</p>
<p>Rather than ssh or the cloud provider's control panel, the server can be started and stopped using cURL.</p>
<p>In the case of DigitalOcean:</p>
<p>Find out the ID of the server:</p>

<pre><code>$ curl \
        -H 'Content-Type: application/json' \
        -H 'Authorization: Bearer '$DIGITAL_OCEAN_ACCESS_TOKEN'' \
    "https://api.digitalocean.com/v2/droplets?name=scheduled-serverless" | jq '.droplets[] | {id:.id, name:.name, status: .status}'</code></pre>
<p>The output gives us the ID</p>

<pre><code>{
    ...
},
{
  "id": 195786885,
  "name": "scheduled-serverless",
  "status": "active"
}</code></pre>
<p>And then power it down:</p>

<pre><code>$ curl -X POST \
    -H 'Content-Type: application/json' \
    -H 'Authorization: Bearer '$DIGITAL_OCEAN_ACCESS_TOKEN'' \
    -d '{"type":"power_off"}' \
    "https://api.digitalocean.com/v2/droplets/195786885/actions" | jq '.[] | {id:.id, status:.status, type:.type}'</code></pre>
<p>Which tells us it's in progress</p>

<pre><code>{
  "id": 955320153,
  "status": "in-progress",
  "type": "power_off"
}</code></pre>
<p>The equivalent cURL for powering on the server is virtual identical</p>

<pre><code>$ curl -X POST \
    -H 'Content-Type: application/json' \
    -H 'Authorization: Bearer '$DIGITAL_OCEAN_ACCESS_TOKEN'' \
    -d '{"type":"power_on"}' \
    "https://api.digitalocean.com/v2/droplets/195786885/actions" | jq '.[] | {id:.id, status:.status, type:.type}'</code></pre>
<p>The web app from the example is running on port 80. We know the server's IP address by referring to the control panel. Let's confirm that the service is running by verifying the output in a browser.</p>
<p><img src="https://raw.githubusercontent.com/jonwhittlestone/words/master/images/do_ip.png" alt=""></p>

</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h2 id="Requests-scripts-in-AWS-Lambda">Requests scripts in AWS Lambda<a class="anchor-link" href="#Requests-scripts-in-AWS-Lambda"> </a></h2><h3 id="The-cURL-statements-in-Python">The cURL statements in Python<a class="anchor-link" href="#The-cURL-statements-in-Python"> </a></h3><p>First, get the ID of the droplet.</p>

</div>
</div>
</div>
    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="kn">import</span> <span class="nn">os</span>
<span class="kn">import</span> <span class="nn">sys</span>
<span class="kn">import</span> <span class="nn">requests</span>

<span class="n">TOKEN</span> <span class="o">=</span> <span class="n">os</span><span class="o">.</span><span class="n">getenv</span><span class="p">(</span><span class="s1">&#39;DIGITAL_OCEAN_ACCESS_TOKEN&#39;</span><span class="p">,</span><span class="s1">&#39;&#39;</span><span class="p">)</span>
<span class="n">DROPLET_NAME</span> <span class="o">=</span> <span class="s1">&#39;scheduled-serverless&#39;</span>

<span class="n">headers</span> <span class="o">=</span> <span class="p">{</span>
    <span class="s1">&#39;Content-Type&#39;</span><span class="p">:</span> <span class="s1">&#39;application/json&#39;</span><span class="p">,</span>
    <span class="s1">&#39;Authorization&#39;</span><span class="p">:</span> <span class="sa">f</span><span class="s1">&#39;Bearer </span><span class="si">{</span><span class="n">TOKEN</span><span class="si">}</span><span class="s1">&#39;</span><span class="p">,</span>
<span class="p">}</span>

<span class="k">if</span> <span class="n">TOKEN</span> <span class="o">==</span> <span class="s1">&#39;&#39;</span><span class="p">:</span>
    <span class="n">sys</span><span class="o">.</span><span class="n">exit</span><span class="p">(</span><span class="s1">&#39;Your environment should have the `DIGITAL_OCEAN_ACCESS_TOKEN exported.&#39;</span><span class="p">)</span>

<span class="k">def</span> <span class="nf">get_droplet</span><span class="p">():</span>
    <span class="n">response</span> <span class="o">=</span> <span class="n">requests</span><span class="o">.</span><span class="n">get</span><span class="p">(</span><span class="s1">&#39;https://api.digitalocean.com/v2/droplets&#39;</span><span class="p">,</span> <span class="n">headers</span><span class="o">=</span><span class="n">headers</span><span class="p">,</span> <span class="n">params</span><span class="o">=</span><span class="p">{})</span>
    <span class="k">return</span> <span class="p">[</span><span class="n">d</span> <span class="k">for</span> <span class="n">d</span> <span class="ow">in</span> <span class="n">response</span><span class="o">.</span><span class="n">json</span><span class="p">()</span><span class="o">.</span><span class="n">get</span><span class="p">(</span><span class="s1">&#39;droplets&#39;</span><span class="p">,{})</span> <span class="k">if</span> <span class="n">d</span><span class="o">.</span><span class="n">get</span><span class="p">(</span><span class="s1">&#39;name&#39;</span><span class="p">,</span><span class="s1">&#39;&#39;</span><span class="p">)</span> <span class="o">==</span> <span class="n">DROPLET_NAME</span><span class="p">][</span><span class="mi">0</span><span class="p">]</span>

<span class="n">DROPLET</span> <span class="o">=</span> <span class="n">get_droplet</span><span class="p">()</span>
<span class="nb">print</span><span class="p">(</span><span class="sa">f</span><span class="s1">&#39;Droplet ID: </span><span class="si">{</span><span class="n">DROPLET</span><span class="o">.</span><span class="n">get</span><span class="p">(</span><span class="s2">&quot;id&quot;</span><span class="p">)</span><span class="si">}</span><span class="s1">&#39;</span><span class="p">)</span>
</pre></div>

    </div>
</div>
</div>

<div class="output_wrapper">
<div class="output">

<div class="output_area">

<div class="output_subarea output_stream output_stdout output_text">
<pre>Droplet ID: 195786885
</pre>
</div>
</div>

</div>
</div>

</div>
    {% endraw %}

<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<p>Now we have the ID, let's define the functions for bringing the servers down and back up.</p>

</div>
</div>
</div>
    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="k">def</span> <span class="nf">power_off</span><span class="p">()</span> <span class="o">-&gt;</span> <span class="nb">dict</span><span class="p">:</span>
    <span class="n">data</span> <span class="o">=</span> <span class="s1">&#39;{&quot;type&quot;:&quot;power_off&quot;}&#39;</span>
    <span class="n">url</span> <span class="o">=</span> <span class="sa">f</span><span class="s1">&#39;https://api.digitalocean.com/v2/droplets/</span><span class="si">{</span><span class="n">DROPLET</span><span class="o">.</span><span class="n">get</span><span class="p">(</span><span class="s2">&quot;id&quot;</span><span class="p">)</span><span class="si">}</span><span class="s1">/actions&#39;</span>
    <span class="n">response</span> <span class="o">=</span> <span class="n">requests</span><span class="o">.</span><span class="n">post</span><span class="p">(</span><span class="n">url</span><span class="p">,</span> <span class="n">headers</span><span class="o">=</span><span class="n">headers</span><span class="p">,</span> <span class="n">data</span><span class="o">=</span><span class="n">data</span><span class="p">)</span>
    <span class="k">return</span> <span class="n">response</span><span class="o">.</span><span class="n">json</span><span class="p">()</span>

<span class="k">def</span> <span class="nf">power_on</span><span class="p">()</span> <span class="o">-&gt;</span> <span class="nb">dict</span><span class="p">:</span>
    <span class="n">data</span> <span class="o">=</span> <span class="s1">&#39;{&quot;type&quot;:&quot;power_on&quot;}&#39;</span>
    <span class="n">url</span> <span class="o">=</span> <span class="sa">f</span><span class="s1">&#39;https://api.digitalocean.com/v2/droplets/</span><span class="si">{</span><span class="n">DROPLET</span><span class="o">.</span><span class="n">get</span><span class="p">(</span><span class="s2">&quot;id&quot;</span><span class="p">)</span><span class="si">}</span><span class="s1">/actions&#39;</span>
    <span class="n">response</span> <span class="o">=</span> <span class="n">requests</span><span class="o">.</span><span class="n">post</span><span class="p">(</span><span class="n">url</span><span class="p">,</span> <span class="n">headers</span><span class="o">=</span><span class="n">headers</span><span class="p">,</span> <span class="n">data</span><span class="o">=</span><span class="n">data</span><span class="p">)</span>
    <span class="k">return</span> <span class="n">response</span><span class="o">.</span><span class="n">json</span><span class="p">()</span>

<span class="n">action_resp</span> <span class="o">=</span> <span class="n">power_off</span><span class="p">()</span>
<span class="nb">print</span><span class="p">(</span><span class="n">action_resp</span><span class="p">[</span><span class="s1">&#39;action&#39;</span><span class="p">][</span><span class="s1">&#39;type&#39;</span><span class="p">])</span>
<span class="nb">print</span><span class="p">(</span><span class="n">action_resp</span><span class="p">[</span><span class="s1">&#39;action&#39;</span><span class="p">][</span><span class="s1">&#39;status&#39;</span><span class="p">])</span>

<span class="nb">print</span><span class="p">(</span><span class="s1">&#39;----&#39;</span><span class="p">)</span>

<span class="c1"># action_resp = power_on()</span>
<span class="c1"># print(action_resp[&#39;action&#39;][&#39;type&#39;])</span>
<span class="c1"># print(action_resp[&#39;action&#39;][&#39;status&#39;])</span>
</pre></div>

    </div>
</div>
</div>

<div class="output_wrapper">
<div class="output">

<div class="output_area">

<div class="output_subarea output_stream output_stdout output_text">
<pre>power_off
in-progress
----
</pre>
</div>
</div>

</div>
</div>

</div>
    {% endraw %}

<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h3 id="Running-the-scripts-in-AWS-Lambda">Running the scripts in AWS Lambda<a class="anchor-link" href="#Running-the-scripts-in-AWS-Lambda"> </a></h3><p>Let's translate the cURL statements into Python.</p>

</div>
</div>
</div>
    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="c1"># Popping on a new on/off timestamp on a firebase stored stack</span>

<span class="kn">import</span> <span class="nn">requests</span>
<span class="kn">import</span> <span class="nn">arrow</span>


<span class="n">start</span><span class="p">:</span> <span class="nb">dict</span> <span class="o">=</span> <span class="p">{</span><span class="s1">&#39;off&#39;</span><span class="p">:</span> <span class="p">[</span><span class="s1">&#39;2020-08-11 09:04&#39;</span><span class="p">],</span> <span class="s1">&#39;on&#39;</span><span class="p">:</span> <span class="p">[</span><span class="s1">&#39;2020-08-11 09:03&#39;</span><span class="p">]}</span>

<span class="c1"># get data</span>
<span class="n">url</span> <span class="o">=</span> <span class="s1">&#39;https://wordsdothowappeddotcom.firebaseio.com/scheduled-serverless-startup.json?print=pretty&#39;</span>
<span class="n">r</span> <span class="o">=</span> <span class="n">requests</span><span class="o">.</span><span class="n">get</span><span class="p">(</span><span class="n">url</span><span class="p">,</span> <span class="n">data</span><span class="o">=</span><span class="p">{},</span> <span class="n">headers</span><span class="o">=</span><span class="p">{},)</span>
<span class="n">data</span> <span class="o">=</span> <span class="n">r</span><span class="o">.</span><span class="n">json</span><span class="p">()</span>
<span class="nb">print</span><span class="p">(</span><span class="n">data</span><span class="p">)</span>

<span class="c1"># replace data</span>
<span class="nb">print</span><span class="p">(</span><span class="n">data</span><span class="p">[</span><span class="s1">&#39;off&#39;</span><span class="p">][</span><span class="mi">0</span><span class="p">])</span>
</pre></div>

    </div>
</div>
</div>

<div class="output_wrapper">
<div class="output">

<div class="output_area">

<div class="output_subarea output_text output_error">
<pre>
<span class="ansi-red-fg">---------------------------------------------------------------------------</span>
<span class="ansi-red-fg">ModuleNotFoundError</span>                       Traceback (most recent call last)
In  <span class="ansi-blue-fg">[12]</span>:
Line <span class="ansi-blue-fg">4</span>:     <span class="ansi-blue-fg">import</span> <span class="ansi-cyan-fg ansi-underline">arrow</span>

<span class="ansi-red-fg">ModuleNotFoundError</span>: No module named &#39;arrow&#39;
<span class="ansi-red-fg">---------------------------------------------------------------------------</span></pre>
</div>
</div>

</div>
</div>

</div>
    {% endraw %}

<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h2 id="Schedule-the-scripts-with-Cloudwatch-events">Schedule the scripts with Cloudwatch events<a class="anchor-link" href="#Schedule-the-scripts-with-Cloudwatch-events"> </a></h2>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h2 id="Putting-it-all-together-in-a-Serverless-Application-Framework-template">Putting it all together in a Serverless Application Framework template<a class="anchor-link" href="#Putting-it-all-together-in-a-Serverless-Application-Framework-template"> </a></h2>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h2 id="Quality-of-Life-enhancements">Quality of Life enhancements<a class="anchor-link" href="#Quality-of-Life-enhancements"> </a></h2><p>When the target service is unavailable, it's quite unfriendly to show a blank 500 screen. A subsequent post outlines a solution for a forwarding service which can display an Open/Closed for business page to the user, with 'Opening times'</p>
<p>As part of what's become known as 'Chatops', it's useful to update team members and stakeholders about the status of the server. A future addition will be to use the Microsoft Teams API to notify interested parties.</p>

</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h1 id="Resources-used:-with-thanks-&#128154;">Resources used: with thanks &#128154;<a class="anchor-link" href="#Resources-used:-with-thanks-&#128154;"> </a></h1><ul>
<li>Curl converter <a href="https://github.com/NickCarneiro/curlconverter">Github repo</a></li>
</ul>
<ul>
<li><p>AWS Lambda and Secret Management Overview <a href="https://epsagon.com/development/aws-lambda-and-secret-management/">Blog post from  Espagon</a></p>
</li>
<li><p>Sharing Secrets with AWS Lambda Using AWS Systems Manager Parameter Store <a href="https://aws.amazon.com/blogs/compute/sharing-secrets-with-aws-lambda-using-aws-systems-manager-parameter-store/">AWS Compute blog post</a></p>
</li>
</ul>

</div>
</div>
</div>
</div>
 
