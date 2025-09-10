---
layout: post
title: "Detroit: A Photo Essay"
author: "Parker A. Hopkins"
date: 2025-09-09
categories: [photography, detroit]
tags: [photo-essay, detroit, gallery]
image: DetroitGallery/IMG_4213.jpg
---

<div class="post-content">
  <h1>{{ page.title }}</h1>

  {% if site.hide_post_date != true %}
    {% include post-date.html %}
  {% endif %}

  <!-- Featured / landing image: uses the DetroitGallery image you requested -->
  <div class="featured-image">
    <img id="landing-image" src="/assets/DetroitGallery/IMG_4213.jpg" alt="Detroit landing photo" loading="eager">
  </div>

  <article>
    <p class="lead">Detroit in light, texture, and edge: a photo essay. The page intentionally loads a single landing image at first to keep the page light. Use the navigation controls to step through the series or load thumbnails on demand.</p>

    <!-- Main viewer: only this <img> is present at initial load -->
    <div id="viewer" aria-live="polite">
      <button id="prevBtn" class="nav-btn" aria-label="Previous photo">‹</button>
      <img id="mainImage" src="" alt="" style="max-width:100%; max-height:70vh; display:block; margin:0 auto;">
      <button id="nextBtn" class="nav-btn" aria-label="Next photo">›</button>
      <div id="caption" style="text-align:center; margin-top:8px; font-size:0.95rem;"></div>
    </div>

    <!-- Optional: load thumbnails only when user asks -->
    <div style="text-align:center; margin:14px 0;">
      <button id="loadThumbsBtn">Load gallery thumbnails</button>
    </div>

    <!-- Thumbnail container will be populated only when user requests it -->
    <div id="thumbContainer" class="thumbnail-grid" aria-hidden="true"></div>

    <!-- Liquid: gather image paths into `images_for_js` -->
    {% assign _gallery = site.static_files | where_exp: "f", "f.path contains 'assets/DetroitGallery'" %}
    {% assign images_for_js = _gallery | sort: "modified_time" | reverse %}
    <script>
      // Build a JS array of image paths from Liquid output: these are strings only,
      // they do not load the images until assigned to an <img>. This keeps the initial
      // page load light even with hundreds of items.
      var images = [
        {% for f in images_for_js %}
          {% assign ext = f.extname | downcase %}
          {% if ext == ".jpg" or ext == ".jpeg" or ext == ".png" or ext == ".webp" %}
            "{{ f.path }}"{% if forloop.last == false %},{% endif %}
          {% endif %}
        {% endfor %}
      ];

      // If there are no images in the folder, guard and show a message.
      if (!images || images.length === 0) {
        document.addEventListener('DOMContentLoaded', function(){
          var cont = document.getElementById('viewer');
          cont.innerHTML = '<p><em>No images found in <code>assets/DetroitGallery</code>. Add images and push to GitHub Pages.</em></p>';
        });
      } else {
        // Initialize viewer with the first image: only this <img> will fetch a resource
        var current = 0;
        var mainImage = null;
        var caption = null;

        function setImage(index){
          index = (index + images.length) % images.length;
          current = index;
          // assign src now: browser will fetch only this image
          mainImage.src = images[current];
          // set alt / caption text from filename
          var name = images[current].split('/').pop().replace(/[-_]/g,' ');
          mainImage.alt = name;
          caption.textContent = name;
        }

        document.addEventListener('DOMContentLoaded', function(){
          mainImage = document.getElementById('mainImage');
          caption = document.getElementById('caption');

          // If you prefer the landing image to be the initial viewer image, find its index:
          var landingPath = '/assets/DetroitGallery/IMG_4213.jpg';
          var landingIndex = images.indexOf(landingPath);
          if (landingIndex >= 0) {
            // show landing image first
            setImage(landingIndex);
          } else {
            // fallback: show images[0]
            setImage(0);
          }

          // Prev/next controls: will set src only when clicked
          document.getElementById('prevBtn').addEventListener('click', function(){ setImage(current - 1); });
          document.getElementById('nextBtn').addEventListener('click', function(){ setImage(current + 1); });

          // Optional keyboard navigation
          document.addEventListener('keydown', function(e){
            if (e.key === 'ArrowLeft') { setImage(current - 1); }
            if (e.key === 'ArrowRight') { setImage(current + 1); }
            if (e.key === 'Home') { setImage(0); }
            if (e.key === 'End') { setImage(images.length - 1); }
          });

          // Lazy thumbnails: build small <img> tags only when user clicks load
          document.getElementById('loadThumbsBtn').addEventListener('click', function(){
            var tc = document.getElementById('thumbContainer');
            if (tc.getAttribute('data-loaded') === 'true') return;
            tc.setAttribute('data-loaded','true');
            tc.setAttribute('aria-hidden','false');

            // Create thumbnail elements without src attributes initially,
            // then populate src with data-src as they scroll into view using IntersectionObserver.
            images.forEach(function(p, i){
              var a = document.createElement('a');
              a.href = p;
              a.className = 'thumb-link';
              a.dataset.index = i;

              var img = document.createElement('img');
              img.dataset.src = p; // actual URL saved to data-src only
              img.alt = p.split('/').pop().replace(/[-_]/g,' ');
              img.className = 'thumb';
              img.style.width = '100%';
              img.style.height = '120px';
              img.style.objectFit = 'cover';
              img.loading = 'lazy';

              a.appendChild(img);
              tc.appendChild(a);

              // click thumbnail to show in main viewer; only then will the full image be fetched
              a.addEventListener('click', function(ev){
                ev.preventDefault();
                setImage(parseInt(this.dataset.index,10));
                window.scrollTo({ top: document.getElementById('viewer').offsetTop - 40, behavior: 'smooth' });
              });
            });

            // IntersectionObserver to swap data-src -> src for thumbnails when visible
            var observer = new IntersectionObserver(function(entries, obs){
              entries.forEach(function(entry){
                if (entry.isIntersecting){
                  var img = entry.target;
                  if (img.dataset.src && !img.src){
                    img.src = img.dataset.src;
                  }
                  obs.unobserve(img);
                }
              });
            }, { rootMargin: '200px 0px', threshold: 0.01 });

            var thumbs = document.querySelectorAll('.thumb');
            thumbs.forEach(function(t){ observer.observe(t); });
          });
        });
      }
    </script>

    <style>
      /* minimal styling; move to your project CSS if preferred */
      .featured-image img { width:100%; height:auto; display:block; border-radius:6px; margin-bottom:0.75rem; }
      #viewer { display:flex; align-items:center; gap:12px; justify-content:center; margin:1rem 0; }
      .nav-btn { background:transparent; border:none; font-size:2rem; cursor:pointer; user-select:none; padding:6px 10px; }
      .thumbnail-grid { display:grid; grid-template-columns: repeat(auto-fill, minmax(140px,1fr)); gap:8px; margin-top:12px; }
      .thumbnail-grid .thumb-link img { border-radius:6px; box-shadow:0 6px 14px rgba(0,0,0,0.12); display:block; }
      #loadThumbsBtn { padding:8px 12px; cursor:pointer; }
    </style>

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
