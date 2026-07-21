---
layout: post
title: "Detroit: A Photo Essay"
author: "Parker A. Hopkins"
date: 2025-09-09
categories: [photography, detroit]
tags: [photo-essay, detroit, gallery]
image: DetroitGallery/IMG_4213.jpg
hide_featured_image: true
---

<p class="lead photo-essay-lead">Detroit in light, texture, and edge.</p>

<section class="photo-essay-gallery" aria-label="Detroit photo gallery">
  <figure class="photo-viewer" aria-live="polite">
    <div id="viewer" class="photo-frame">
      <button id="prevBtn" class="nav-btn" type="button" aria-label="Previous photo">&lsaquo;</button>
      <img id="mainImage" src="/assets/DetroitGallery/IMG_4213.jpg" alt="Detroit photo" loading="eager" decoding="async">
      <button id="nextBtn" class="nav-btn" type="button" aria-label="Next photo">&rsaquo;</button>
    </div>
    <figcaption id="caption" class="photo-caption"></figcaption>
  </figure>

  <div class="gallery-actions">
    <button id="loadThumbsBtn" class="gallery-button" type="button" aria-controls="thumbContainer" aria-expanded="false">Show thumbnails</button>
  </div>

  <div id="thumbContainer" class="thumbnail-grid" aria-hidden="true"></div>
</section>

{% assign _gallery = site.static_files | where_exp: "f", "f.path contains '/assets/DetroitGallery/'" | sort: "path" %}
<script>
  var images = [
    {% for f in _gallery %}
      {% assign ext = f.extname | downcase %}
      {% if ext == ".jpg" or ext == ".jpeg" or ext == ".png" or ext == ".webp" %}
        "{{ f.path }}"{% unless forloop.last %},{% endunless %}
      {% endif %}
    {% endfor %}
  ].filter(Boolean);

  document.addEventListener('DOMContentLoaded', function () {
    var viewer = document.getElementById('viewer');
    var mainImage = document.getElementById('mainImage');
    var caption = document.getElementById('caption');
    var thumbsButton = document.getElementById('loadThumbsBtn');
    var thumbContainer = document.getElementById('thumbContainer');
    var featuredPath = '/assets/DetroitGallery/IMG_4213.jpg';
    var current = 0;

    if (!images.length) {
      viewer.innerHTML = '<p><em>No images found in <code>assets/DetroitGallery</code>.</em></p>';
      caption.textContent = '';
      thumbsButton.disabled = true;
      return;
    }

    current = Math.max(images.indexOf(featuredPath), 0);

    function updateActiveThumb() {
      if (!thumbContainer || thumbContainer.dataset.loaded !== 'true') return;

      var activeThumb = thumbContainer.querySelector('[aria-current="true"]');
      var nextThumb = thumbContainer.querySelector('[data-index="' + current + '"]');

      if (activeThumb) activeThumb.removeAttribute('aria-current');
      if (nextThumb) nextThumb.setAttribute('aria-current', 'true');
    }

    function setImage(index) {
      current = (index + images.length) % images.length;

      if (mainImage.getAttribute('src') !== images[current]) {
        mainImage.src = images[current];
      }

      mainImage.alt = 'Detroit photo ' + (current + 1) + ' of ' + images.length;
      caption.textContent = 'Photo ' + (current + 1) + ' of ' + images.length;
      updateActiveThumb();
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
      if (event.key === 'Home') setImage(0);
      if (event.key === 'End') setImage(images.length - 1);
    });

    thumbsButton.addEventListener('click', function () {
      if (thumbContainer.dataset.loaded === 'true') return;

      var observer = 'IntersectionObserver' in window ? new IntersectionObserver(function (entries, obs) {
        entries.forEach(function (entry) {
          if (!entry.isIntersecting) return;

          var img = entry.target;
          if (img.dataset.src && !img.src) img.src = img.dataset.src;
          obs.unobserve(img);
        });
      }, { rootMargin: '200px 0px', threshold: 0.01 }) : null;

      thumbContainer.dataset.loaded = 'true';
      thumbContainer.setAttribute('aria-hidden', 'false');
      thumbsButton.setAttribute('aria-expanded', 'true');
      thumbsButton.textContent = 'Thumbnails shown';
      thumbsButton.disabled = true;

      images.forEach(function (path, index) {
        var link = document.createElement('a');
        var thumb = document.createElement('img');

        link.href = path;
        link.className = 'thumb-link';
        link.dataset.index = index;
        link.setAttribute('aria-label', 'Show Detroit photo ' + (index + 1) + ' of ' + images.length);

        thumb.dataset.src = path;
        thumb.alt = 'Detroit photo ' + (index + 1) + ' of ' + images.length;
        thumb.className = 'thumb';
        thumb.loading = 'lazy';
        thumb.decoding = 'async';

        link.appendChild(thumb);
        thumbContainer.appendChild(link);

        if (observer) {
          observer.observe(thumb);
        } else {
          thumb.src = path;
        }

        link.addEventListener('click', function (event) {
          event.preventDefault();
          setImage(index);
          window.scrollTo({ top: viewer.offsetTop - 40, behavior: 'smooth' });
        });
      });

      updateActiveThumb();
    });

    setImage(current);
  });
</script>
