---
layout: default
title: Write-ups
permalink: /writeups/
---

<div class="writeups-container">
  {% for writeup in site.writeups %}
  <details class="writeup-card">
    <summary>
      <div class="summary-wrapper">
        <div class="header-top">
          <h3 class="writeup-title">{{ writeup.title }}</h3>
          <span class="level-badge {{ writeup.level | downcase }}">{{ writeup.level }}</span>
        </div>
        <p class="writeup-description">{{ writeup.description }}</p>
      </div>
    </summary>

    <div class="writeup-content">
      <div class="content-divider"></div>
      {{ writeup.content | markdownify }}
    </div>
  </details>
  {% endfor %}
</div>

<style>
.writeups-container {
  max-width: 800px;
  margin: 0 auto;
}

/* The Card Styling */
.writeup-card {
  background: #111; /* Sleek dark background */
  border: 1px solid #333;
  border-radius: 12px;
  margin-bottom: 16px;
  transition: all 0.3s ease;
  list-style: none;
}

/* Make the whole top area clickable */
summary {
  display: block; /* Ensures it acts like a block element */
  padding: 24px;
  cursor: pointer;
  outline: none;
  list-style: none;
}

summary::-webkit-details-marker {
  display: none; /* Hide default arrow */
}

/* Hover effect on the whole card */
.writeup-card:hover {
  border-color: #64ffda;
  background: #161616;
}

/* Open state styling */
.writeup-card[open] {
  border-color: #64ffda;
  background: #161616;
}

.header-top {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 12px;
}

.writeup-title {
  font-size: 1.4rem;
  margin: 0;
  color: #fff;
  font-weight: 600;
}

.writeup-description {
  color: #999;
  margin: 0;
  font-size: 1.05rem;
  line-height: 1.6;
}

/* Badges */
.level-badge {
  padding: 4px 10px;
  border-radius: 6px;
  font-size: 0.7rem;
  font-weight: 700;
  letter-spacing: 0.5px;
  text-transform: uppercase;
}

.level-badge.beginner { background: rgba(46, 125, 50, 0.2); color: #81c784; border: 1px solid #2e7d32; }
.level-badge.intermediate { background: rgba(245, 124, 0, 0.2); color: #ffb74d; border: 1px solid #f57c00; }
.level-badge.hard { background: rgba(211, 47, 47, 0.2); color: #e57373; border: 1px solid #d32f2f; }

/* Expanded Content */
.writeup-content {
  padding: 0 24px 24px 24px;
}

.content-divider {
  height: 1px;
  background: #333;
  margin-bottom: 20px;
}


</style>