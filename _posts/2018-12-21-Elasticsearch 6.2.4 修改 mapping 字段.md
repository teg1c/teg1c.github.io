---
layout: mypost
title: Elasticsearch 6.2.4 修改 mapping 字段
categories: [elasticsearch]
---

Elasticsearch 的 mapping 一旦创建，只能增加字段，而不能修改已经 mapping 的字段。但现实往往并非如此啊，有时增加一个字段，就好像打了一个补丁，一个可以，但是越补越多，最后自己都觉得惨不忍睹了。怎么办？？

这里有一个方法修改 mapping ，那就是重新建立一个 index ，然后创建一个新的 mapping 。你可能会问，这要是在生产环境，可行吗？答案是，如果你一开始就采取了合适的设计，这个完全是可以做到平滑过渡的。

采取什么合理设计呢？就是我们的程序访问索引库时，始终使用同义词来访问，而不要使用真正的 indexName 。在 reindex 完数据之后，修改之前的同义词即可。明白了吗？


----------


步骤一
---
新建一个索引，字段和原字段一致，只修改需要的字段。


----------


步骤二
---
把原数据给复制到新索引中。小规模数据可以直接用es内置的代码

    POST IP:PORT/_reindex
    {
      "source": {
        "index": "listenday",//老的index
        "type": "listenday"//老的type
      },
      "dest": {
        "index": "my_index",//新的index
        "type": "my_index"//老的type
      }
    }


----------


步骤三
---
删除老的索引

    curl -XDELETE IP:PORT/listenday

大功告成！！！


----------


结语
---
采用标准的重建索引方式的时候，推荐大家为每一个type都建立一个索引同义词，即便在同一个索引库中的多个type，也推荐使用建立一个同义词来访问。即一个index里面包含一个type，因为在elasticsearch中，跨index查询数据是很方便的。这样，我们就可以在reindex一个type后，立即将type生效，而不是将index下面所有的type都重建完后，同义词才能生效。