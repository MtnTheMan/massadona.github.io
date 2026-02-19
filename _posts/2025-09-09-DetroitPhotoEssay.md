---
layout: post
title: "Detroit: A Photo Essay"
author: "Parker A. Hopkins"
date: 2025-09-09
categories: [photography, detroit]
tags: [photo-essay, detroit, gallery]
image: DetroitGallery/IMG_4213.jpg
---

<p class="lead">Detroit in light, texture, and edge. Use the arrows or your keyboard to browse one photo at a time. Thumbnails are loaded only when requested.</p>

<div id="viewer" aria-live="polite">
  <button id="prevBtn" class="nav-btn" aria-label="Previous photo">‹</button>
  <img id="mainImage" src="/assets/DetroitGallery/IMG_4213.jpg" alt="Detroit photo" style="max-width:100%; max-height:70vh; display:block; margin:0 auto;">
  <button id="nextBtn" class="nav-btn" aria-label="Next photo">›</button>
</div>
<div id="caption" style="text-align:center; margin-top:8px; font-size:0.95rem;"></div>

<div style="text-align:center; margin:14px 0;">
  <button id="loadThumbsBtn" type="button">Load gallery thumbnails</button>
</div>

<div id="thumbContainer" class="thumbnail-grid" aria-hidden="true"></div>

{% assign _gallery = site.static_files | where_exp: "f", "f.path contains 'assets/DetroitGallery/'" %}
<script>
  var images = [
    {% for f in _gallery %}
      "{{ f.path }}"{% unless forloop.last %},{% endunless %}
    {% endfor %}
  ].filter(function(path){
    return /\.(jpg|jpeg|png|webp)$/i.test(path);
  });

  document.addEventListener('DOMContentLoaded', function () {
    var viewer = document.getElementById('viewer');
    var mainImage = document.getElementById('mainImage');
    var caption = document.getElementById('caption');
    var thumbsButton = document.getElementById('loadThumbsBtn');
    var thumbContainer = document.getElementById('thumbContainer');

    if (!images.length) {
      viewer.innerHTML = '<p><em>No images found in <code>assets/DetroitGallery</code>.</em></p>';
      caption.textContent = '';
      thumbsButton.disabled = true;
      return;
    }

    var current = Math.max(images.indexOf('/assets/DetroitGallery/IMG_4213.jpg'), 0);

    function imageLabel(path) {
      return path.split('/').pop().replace(/[-_]/g, ' ');
    }

    function setImage(index) {
      current = (index + images.length) % images.length;
      mainImage.src = images[current];
      mainImage.alt = imageLabel(images[current]);
      caption.textContent = (current + 1) + ' / ' + images.length + ' — ' + imageLabel(images[current]);
    }

    document.getElementById('prevBtn').addEventListener('click', function () {
      setImage(current - 1);
    });

    document.getElementById('nextBtn').addEventListener('click', function () {
      setImage(current + 1);
    });

    document.addEventListener('keydown', function (event) {
      if (event.key === 'ArrowLeft') setImage(current - 1);
      if (event.key === 'ArrowRight') setImage(current + 1);
    });

    thumbsButton.addEventListener('click', function () {
      if (thumbContainer.dataset.loaded === 'true') return;

      thumbContainer.dataset.loaded = 'true';
      thumbContainer.setAttribute('aria-hidden', 'false');

      images.forEach(function (path, index) {
        var link = document.createElement('a');
        var thumb = document.createElement('img');

        link.href = path;
        link.className = 'thumb-link';
        link.dataset.index = index;

        thumb.dataset.src = path;
        thumb.alt = imageLabel(path);
        thumb.className = 'thumb';
        thumb.loading = 'lazy';
        thumb.style.width = '100%';
        thumb.style.height = '120px';
        thumb.style.objectFit = 'cover';

        link.appendChild(thumb);
        thumbContainer.appendChild(link);

        link.addEventListener('click', function (event) {
          event.preventDefault();
          setImage(index);
          window.scrollTo({ top: viewer.offsetTop - 40, behavior: 'smooth' });
        });
      });

      var observer = new IntersectionObserver(function (entries, obs) {
        entries.forEach(function (entry) {
          if (!entry.isIntersecting) return;
          var img = entry.target;
          if (img.dataset.src && !img.src) img.src = img.dataset.src;
          obs.unobserve(img);
        });
      }, { rootMargin: '200px 0px', threshold: 0.01 });

      document.querySelectorAll('.thumb').forEach(function (thumb) {
        observer.observe(thumb);
      });
    });

    setImage(current);
  });
</script>

<style>
  #viewer { display:flex; align-items:center; gap:12px; justify-content:center; margin:1rem 0; }
  .nav-btn { background:transparent; border:none; font-size:2rem; cursor:pointer; user-select:none; padding:6px 10px; }
  .thumbnail-grid { display:grid; grid-template-columns: repeat(auto-fill, minmax(140px,1fr)); gap:8px; margin-top:12px; }
  .thumbnail-grid .thumb-link img { border-radius:6px; box-shadow:0 6px 14px rgba(0,0,0,0.12); display:block; }
  #loadThumbsBtn { padding:8px 12px; cursor:pointer; }
</style>
