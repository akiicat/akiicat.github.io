# Hi

```
puts "hello world!"
```

{{ site.static_files }}


![hi](/螢幕快照 2016-07-30 下午11.05.09.png)

{% for image in site.static_files %}
    {{image.path}} - {{image.modified_time}}
    ![hi]({{site.baseurl}}{{image.path}})
{% endfor %}


{{ site.posts }}
