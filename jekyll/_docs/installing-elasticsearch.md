---
layout: classic-docs
title: Install a custom version of Elasticsearch
description: Installing a custom version of Elasticsearch
---

CircleCI supports a large number of
[services](/docs/environment/#databases) which can be enabled from a circle.yml file checked into your repo's root directory. To enable Elasticsearch, add the following to your circle.yml:

```
machine:
  services:
    - elasticsearch
```

The default version of elasticsearch is {{ site.data.versions.elasticsearch }}.
If you need a custom version, you can download and start it from your build. To install 1.0.1, add the following to your circle.yml:

```
dependencies:
  post:
    - wget https://download.elastic.co/elasticsearch/elasticsearch/elasticsearch-1.0.1.tar.gz
    - tar -xvf elasticsearch-1.0.1.tar.gz
    - elasticsearch-1.0.1/bin/elasticsearch: {background: true}
```

<span class='label label-info'>Note:</span>
remember to remove elasticsearch from machine.services if you install it manually.

## Install an Elasticsearch plugin

It's easy to install a plugin from a url, just add a command to install the plugin before you start elasticsearch:

```
dependencies:
  post:
    - wget https://download.elastic.co/elasticsearch/elasticsearch/elasticsearch-1.0.1.tar.gz
    - tar -xvf elasticsearch-1.0.1.tar.gz

    - elasticsearch-1.0.1/bin/plugin --url https://example.com/plugin.zip --install example-plugin

    - elasticsearch-1.0.1/bin/elasticsearch: {background: true}

```

## Caching

Downloading Elasticsearch can take time, making your build longer.
To reduce the time spent installing dependencies, CircleCI can cache them between builds.
You can add arbitrary directories to the cache, allowing you to avoid the overhead of building your custom software during the build.

Tell CircleCI to save a cached copy using the
[`cache_directories` settings in your `circle.yml` file](/docs/configuration/#cache-directories).
Then check for the directory before you download elasticsearch:

```
dependencies:
  cache_directories:
    - elasticsearch-1.0.1 # relative to the build directory
  post:
    - if [[ ! -e elasticsearch-1.0.1 ]]; then wget https://download.elastic.co/elasticsearch/elasticsearch/elasticsearch-1.0.1.tar.gz && tar -xvf elasticsearch-1.0.1.tar.gz; fi
    - elasticsearch-1.0.1/bin/elasticsearch: {background: true}
```
