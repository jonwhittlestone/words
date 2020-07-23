---
toc: true
layout: post
description: Data rates of various systems
categories: [cheatsheet, python performance]
title: Computers are fast - A cheatsheet
image: http://i.imgur.com/pkQnonr.png
hide: false
---

<style>
table {
    width:100%;
}
</style>

This is inspired by Julia Evans and Kamal Marhubi's excellent mini-quiz on the data rates of systems and components.

The below cheatsheet serves as a summary of those benchmark speeds, for at-a-glance consumption.

## Orders of magnitude

The idea is not necessarily know the exact figures for a particular benchmark, just the right order of magnitude.

🐌 10<sup>0</sup> 1

🦥 10<sup>1</sup> 10

🚶 10<sup>2</sup> 100

🏃‍♀️ 10<sup>3</sup> 1,000

🏇‍ 10<sup>4</sup> 10,000

‍🏎️💨 10<sup>5</sup> 100,000

🚁 10<sup>6</sup> 1,000,000

✈️ 10<sup>7</sup> 10,000,000

🚀 10<sup>8</sup> 100,000,000

🌠 10<sup>9</sup> 1,000,000,000

## 'How much can be done in a second'

### [`sum.c`](https://github.com/jonwhittlestone/one-second/blob/master/benchmarks/sum.c)
* Each loop adds to total
* How many loop iterations per second?
    * 🚀 - 🌠
    * Between 100 million and 1 Billion
    * 10<sup>8</sup> > and < 10<sup>9</sup>

### [`loop.py`](https://github.com/jonwhittlestone/one-second/blob/master/benchmarks/loop.py)
* Empty loop contents
* How many loop iterations per second?
    * ✈️ - 🚀
    * Between 10m and 100m
    * 10<sup>7</sup> > and < 10<sup>8</sup>

### [`dict.py`](https://github.com/jonwhittlestone/one-second/blob/master/benchmarks/dict.py)
* Add elements to a fixed-size dictionary
* How many loop iterations per second?
    * ✈️ - 🚀
    * Between 10m and 100m
    * 10<sup>4</sup> > and < 10<sup>5</sup>

### [`parse_http_request.py`](https://github.com/jonwhittlestone/one-second/blob/master/benchmarks/parse_http_request.py)
* Using Python built-in HTTP request parser
* How many HTTP requests parsed per second?
    *  🏇‍️ - ‍🏎️💨‍
    * Between 10,000 and 100,000
    * 10<sup>4</sup> > and < 10<sup>5</sup>

### [`download_webpage.py`](https://github.com/jonwhittlestone/one-second/blob/master/benchmarks/download_webpage.py)
* Downloading with urllib2
* How many HTTP requests completed per second?
    *  🐌‍️ - ‍🦥
    * Between 1 and 10
    * > The true cost depends on size, connection speed and distance from servers

### [`run_python.sh`](https://github.com/jonwhittlestone/one-second/blob/master/benchmarks/run_python.sh)
* Running Python script(s) from bash
* Bash loop iterations per second
    *  🦥 - ‍🚶‍
    * Between 10 and 100
    * 10<sup>1</sup> > and < 10<sup>2</sup>
    
### [`write_to_disk.py`](https://github.com/jonwhittlestone/one-second/blob/master/benchmarks/write_to_disk.py)
* Code run on a machine with an SSD
* How many bytes written to disk in a second 
    * 🚀 - 🌠
    * Between 100 million and 1 Billion
* How many megabytes written to disk in a second 
    * 🚶 - 🏃‍♀️
    * Between 100 and 1,000
    * My T480s running Ubuntu with SSD reports write speed: 2.3 GB/s and read 673 MB/s [^1]

### [`write_to_memory.py`](https://github.com/jonwhittlestone/one-second/blob/master/benchmarks/write_to_memory.py)
* How many bytes written to a string in memory in a second
    * 🌠+
    * Above 1 Billion

### [`grep_bytes.sh`](https://github.com/jonwhittlestone/one-second/blob/master/benchmarks/grep_bytes.sh)
* Bytes search in a second
    * 🌠+
    * Above 1 Billion
    

### [`find-filenames.sh`](https://github.com/jonwhittlestone/one-second/blob/master/benchmarks/find-filenames.sh)
* Files come from the filesystem cache
* How many files listed in one second?
    *  🏎💨 - ‍🚁
    * Between 100,000 and 1m
    * 10<sup>5</sup> > and < 10<sup>6</sup>
    * > Grep can search at 2GB/s so more limited by disk speed than grep’s speed

### [`json_parse.py`](https://github.com/jonwhittlestone/one-second/blob/master/benchmarks/json-parse.py)
* Deserializing the same 65k of JSON repeatedly
* How many loop iterations in a second?
    * 🚶 - ‍🏃‍♀️
    * Between 100 and 1,000
    * 10<sup>2</sup> > and < 10<sup>3</sup>

### [`msgpack_parse.py`](https://github.com/jonwhittlestone/one-second/blob/master/benchmarks/msgpack_parse.py)
* Parse 46k of msgpack data
* How many loop iterations in a second?
    * 🏃‍♀️ - ‍🏇‍
    * Between 1,000 and 10,000
    * 10<sup>3</sup> > and < 10<sup>4</sup>
    * > Your choice of format and deserialisation library (eg capnproto) makes a big difference


### [`database_indexed.py`](https://github.com/jonwhittlestone/one-second/blob/master/benchmarks/database_indexed.py)
* Select one row from an indexed sqlite table containing 10m rows
* How many queries in one second
    * ‍🏇‍ - 🏎️💨
    * Between 10,000 and 100,000
    * 10<sup>4</sup> > and < 10<sup>5</sup>
    * Typical query in 20 microseconds

### [`database_unindexed.py`](https://github.com/jonwhittlestone/one-second/blob/master/benchmarks/database_unindexed.py)
* Select one row from an unindexed sqlite table containing 10m rows
* How many queries in one second
    *  🐌‍️ - ‍🦥
    * Between 1 and 10
    * 10<sup>0</sup> > and < 10<sup>1</sup>

### [`hash.py`](https://github.com/jonwhittlestone/one-second/blob/master/benchmarks/hash.py)
* How many bytes hashed in one second?
    * 🚀 - 🌠
    * Between 100 million and 1 Billion
    * 10<sup>8</sup> > and < 10<sup>9</sup>
    * > MD5 is designed to be fast

### [`bcrypt_hash.py`](https://github.com/jonwhittlestone/one-second/blob/master/benchmarks/bcrypt_hash.py)
* Number of passwords hashed in a second?
    *  🐌‍️ - ‍🦥
    * Between 1 and 10
    * 10<sup>0</sup> > and < 10<sup>1</sup>

### [`fill_array.c`](https://github.com/jonwhittlestone/one-second/blob/master/benchmarks/fill_array.c)
* Memory is accessed sequentially from a CPU cache
* Number of passwords hashed in a second?
    * 🚀 - 🌠
    * Between 100 million and 1 Billion
    * 10<sup>8</sup> > and < 10<sup>9</sup>

### [`fill_array.c`](https://github.com/jonwhittlestone/one-second/blob/master/benchmarks/fill_array.c)
* Number of passwords hashed in a second?
    * ✈️ - 🚀
    * Between 10m and 100m
    * 10<sup>4</sup> > and < 10<sup>5</sup>

## Resources used: with thanks 💚

* Do you know how much your computer can do in a second? - [Mini Quiz](http://computers-are-fast.github.io/) 

[^1]: [https://www.binarytides.com/linux-test-drive-speed/](https://www.binarytides.com/linux-test-drive-speed/)
