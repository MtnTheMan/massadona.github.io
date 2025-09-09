---
layout: post
title: "Detroit: A Photo Essay"
author: "Parker A. Hopkins"
date: 2025-09-09
categories: [photography, detroit]
tags: [photo-essay, detroit, gallery]
---

<div class="post-content">
  <h1>{{ page.title }}</h1>

  {% if site.hide_post_date != true %}
    {% include post-date.html %}
  {% endif %}

  <article>
    <p class="lead">Detroit in light, texture, and edge — a short photo essay drawn from images inside the site's asset folder. Click any thumbnail to view the full-size photo and navigate the series.</p>

    <!-- Gallery grid (automatically reads images from assets/DetroitGallery) -->
    <div id="detroit-photo-essay">
      {% assign gallery = site.static_files | where_exp: "f", "f.path contains 'assets/DetroitGallery'" %}
      {% assign images = gallery | sort: "modified_time" | reverse %}
      <div class="gallery-grid">
        {% for image in images %}
          {% assign ext = image.extname | downcase %}
          {% if ext == ".jpg" or ext == ".jpeg" or ext == ".png" or ext == ".webp" %}
            <a href="{{ image.path }}" class="gallery-item" data-caption="{{ image.name | replace: '-', ' ' | replace: '_', ' ' }}">
              <img src="{{ image.path }}" alt="{{ image.name }}" loading="lazy">
            </a>
          {% endif %}
        {% endfor %}
        {% if images == empty %}
          <p><em>No images found in <code>assets/DetroitGallery</code>. Add images there and push to GitHub; they will appear automatically.</em></p>
        {% endif %}
      </div>
    </div>

    <!-- Lightbox modal -->
    <div id="lb" aria-hidden="true" class="lightbox">
      <button id="lb-close" class="lb-btn lb-close" aria-label="Close">×</button>
      <button id="lb-prev"  class="lb-btn lb-prev"  aria-label="Previous">‹</button>
      <img id="lb-image" src="" alt="">
      <div id="lb-caption" class="lb-caption"></div>
      <button id="lb-next"  class="lb-btn lb-next"  aria-label="Next">›</button>
    </div>

    <style>
      /* Inline gallery styles (you can move these to your site CSS) */
      .gallery-grid {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
        gap: 12px;
        margin: 1.25rem 0;
      }
      .gallery-item img {
        width: 100%;
        height: 200px;
        object-fit: cover;
        border-radius: 6px;
        box-shadow: 0 6px 14px rgba(0,0,0,0.12);
        display: block;
      }
      .lead { margin-bottom: 1rem; font-size: 1.05rem; line-height: 1.4; }

      /* Lightbox */
      .lightbox {
        display: none;
        position: fixed;
        inset: 0;
        background: rgba(0,0,0,0.88);
        z-index: 9999;
        align-items: center;
        justify-content: center;
        padding: 20px;
        gap: 8px;
        flex-direction: column;
      }
      .lightbox img {
        max-width: 95%;
        max-height: 80vh;
        border-radius: 4px;
      }
      .lb-caption {
        color: #fff;
        font-size: 0.95rem;
        margin-top: 8px;
        text-align: center;
        max-width: 90%;
        overflow-wrap: anywhere;
      }
      .lb-btn {
        position: absolute;
        background: transparent;
        color: #fff;
        border: none;
        font-size: 2.2rem;
        cursor: pointer;
        padding: 6px 10px;
        user-select: none;
      }
      .lb-close { top: 10px; right: 14px; }
      .lb-prev  { left: 8px; top: 50%; transform: translateY(-50%); }
      .lb-next  { right: 8px; top: 50%; transform: translateY(-50%); }
      @media (max-width:640px){
        .gallery-item img { height: 140px; }
      }
    </style>

    <script>
      (function(){
        var items = Array.from(document.querySelectorAll('.gallery-item'));
        if (!items.length) return;

        var lb = document.getElementById('lb');
        var lbImage = document.getElementById('lb-image');
        var lbCaption = document.getElementById('lb-caption');
        var closeBtn = document.getElementById('lb-close');
        var prevBtn = document.getElementById('lb-prev');
        var nextBtn = document.getElementById('lb-next');

        var currentIndex = 0;

        function show(index){
          var a = items[index];
          lbImage.src = a.href;
          lbImage.alt = a.dataset.caption || '';
          lbCaption.textContent = a.dataset.caption || '';
          lb.style.display = 'flex';
          lb.setAttribute('aria-hidden','false');
          currentIndex = index;
          document.body.style.overflow = 'hidden';
        }
        function hide(){
          lb.style.display = 'none';
          lb.setAttribute('aria-hidden','true');
          lbImage.src = '';
          document.body.style.overflow = '';
        }
        function prev(){
          show((currentIndex - 1 + items.length) % items.length);
        }
        function next(){
          show((currentIndex + 1) % items.length);
        }

        items.forEach(function(a, i){
          a.addEventListener('click', function(e){
            e.preventDefault();
            show(i);
          });
        });

        closeBtn.addEventListener('click', hide);
        prevBtn.addEventListener('click', prev);
        nextBtn.addEventListener('click', next);

        lb.addEventListener('click', function(e){
          if (e.target === lb) hide();
        });

        document.addEventListener('keydown', function(e){
          if (lb.style.display !== 'flex') return;
          if (e.key === 'Escape') hide();
          if (e.key === 'ArrowLeft') prev();
          if (e.key === 'ArrowRight') next();
        });
      })();
    </script>

  </article>

  {% if site.hide_post_share != true %}
    {% include post-share.html %}
  {% endif %}
  {% if site.hide_related_posts != true %}
    {% include related-posts.html %}
  {% endif %}
  {% if site.data.settings.disqus.comments %}
    {% include disqus.html %}
  {% endif %}
</div>
