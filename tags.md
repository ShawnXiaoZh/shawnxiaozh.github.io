---
layout: page
title: Tags
permalink: /tags/
---

{% assign tag_names = site.tags | sort %}
{% if tag_names and tag_names.size > 0 %}
  <p>Choose any tag below to focus on the posts filed under it.</p>

  <div class="tag-filter">
    {% for tag in tag_names %}
      {% assign tag_name = tag[0] %}
      {% assign tag_slug = tag_name | slugify %}
      <button class="tag-chip{% if forloop.first %} is-active{% endif %}" data-tag="{{ tag_slug }}">
        {{ tag_name }} <span>{{ tag[1] | size }}</span>
      </button>
    {% endfor %}
  </div>

  <div class="tag-panels">
    {% for tag in tag_names %}
      {% assign tag_name = tag[0] %}
      {% assign tag_slug = tag_name | slugify %}
      {% assign posts_in_tag = tag[1] %}
      <section class="tag-panel{% if forloop.first %} is-active{% endif %}" data-tag="{{ tag_slug }}">
        <h2 id="{{ tag_slug }}">{{ tag_name }} <span>({{ posts_in_tag | size }})</span></h2>
        <ul>
          {% for post in posts_in_tag %}
            <li>
              <span class="post-meta">{{ post.date | date: "%Y-%m-%d" }}</span>
              <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
            </li>
          {% endfor %}
        </ul>
      </section>
    {% endfor %}
  </div>

  <script>
    document.addEventListener('DOMContentLoaded', function() {
      var chips = Array.prototype.slice.call(document.querySelectorAll('.tag-chip'));
      var panels = Array.prototype.slice.call(document.querySelectorAll('.tag-panel'));
      var panelContainer = document.querySelector('.tag-panels');
      if (!chips.length || !panels.length || !panelContainer) {
        return;
      }

      function activate(tagSlug) {
        chips.forEach(function(chip) {
          chip.classList.toggle('is-active', chip.dataset.tag === tagSlug);
        });
        panels.forEach(function(panel) {
          panel.classList.toggle('is-active', panel.dataset.tag === tagSlug);
        });
      }

      panelContainer.classList.add('js-ready');

      var initial = window.location.hash ? window.location.hash.replace('#', '') : '';
      var initialMatch = panels.find(function(panel) { return panel.dataset.tag === initial; });
      var defaultTag = initialMatch ? initial : (chips[0] ? chips[0].dataset.tag : '');

      activate(defaultTag);

      chips.forEach(function(chip) {
        chip.addEventListener('click', function() {
          var targetTag = chip.dataset.tag;
          activate(targetTag);
          if (history.pushState) {
            history.replaceState(null, '', '#' + targetTag);
          } else {
            window.location.hash = targetTag;
          }
        });
      });
    });
  </script>
{% else %}
  <p>No tagged posts yet.</p>
{% endif %}
