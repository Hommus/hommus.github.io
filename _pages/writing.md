---
layout: default
---

<section class="posts">
    <ul>
    {% for post in site.categories.writing %}
        <li>
            <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
            <time datetime="{{ post.date | date_to_xmlschema }}">
                {{ post.date | date: "%m-%d-%Y" }}
            </time>
        </li>
    {% endfor %}
    </ul>
</section>