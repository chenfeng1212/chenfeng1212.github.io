---
layout: default
title: projects
permalink: /projects/
nav: true
nav_order: 3

# Sections on this page, in order. Each project picks one with "category:" in its file (_projects/*.md).
sections:
  - key: research
    title: Research Projects
  - key: maker
    title: Maker & Course Projects

# Smaller course projects listed at the bottom (no picture needed)
more_projects:
  - name: PIC Tetris 2 You
    note: Microcomputer course · C on PIC
  - name: VPP Real-Time Scheduling System
    note: Real-Time Systems course · Python
  - name: FPGA VGA Bouncing-Ball Game
    note: Digital Systems course · Verilog
  - name: BiBi BoBo
    note: Coin-sorting piggy bank, designed from scratch with 3D printing and laser cutting
---

{% assign total = site.projects | size %}

<div class="projects-page">
  <header class="projects-intro">
    <p class="eyebrow">{{ total }} projects · {{ page.sections | size }} categories</p>
    <h1 class="projects-title">Things I've <span class="highlight-mark">built</span>.</h1>
    <p class="projects-lead">From devices you hold and play, to the hardware and chip design tools underneath them.</p>
  </header>

  {% for section in page.sections %}
    {% assign items = site.projects | where: 'category', section.key | sort: 'date' | reverse %}
    {% if items.size > 0 %}
      <section class="proj-section">
        <div class="proj-section-head">
          <span class="section-label">0{{ forloop.index }}</span>
          <h2>{{ section.title }}</h2>
        </div>

        {% for p in items %}
          <article class="proj" id="{{ p.slug }}">
            <div class="proj-thumb offset-frame">
              {% if p.img %}
                {% if p.detail %}<a href="{{ p.url | relative_url }}" class="proj-thumb-link">{% endif %}
                <img src="{{ p.img | relative_url }}" alt="{{ p.title }}" loading="lazy">
                {% if p.detail %}</a>{% endif %}
              {% else %}
                <div class="frame-inner proj-thumb-empty" aria-hidden="true">{{ p.title }}</div>
              {% endif %}
            </div>
            <div class="proj-body">
              {% if p.tags %}
                <div class="chip-row">
                  {% for t in p.tags %}<span class="chip">{{ t }}</span>{% endfor %}
                </div>
              {% endif %}
              <h3 class="proj-title">{% if p.detail %}<a href="{{ p.url | relative_url }}">{{ p.title }}</a>{% else %}{{ p.title }}{% endif %}</h3>
              {% if p.subtitle %}<p class="proj-subtitle">{{ p.subtitle }}</p>{% endif %}
              <p class="proj-meta">
                {{ p.venue }}{% if p.venue %} · {% endif %}{{ p.date | date: '%Y.%m' }}
              </p>
              {% if p.team %}<p class="proj-team">{{ p.team }}</p>{% endif %}
              <div class="proj-desc">{% if p.summary %}{{ p.summary | markdownify }}{% else %}{{ p.content | markdownify }}{% endif %}</div>
              {% if p.tech and p.tech.size > 0 %}
                <p class="proj-tech">{{ p.tech | join: ' · ' }}</p>
              {% endif %}
              {% if p.detail or p.links.size > 0 %}
                <div class="btn-row">
                  {% if p.detail %}<a class="btn-solid btn-small" href="{{ p.url | relative_url }}">Read more →</a>{% endif %}
                  {% for l in p.links %}
                    <a class="btn-outline btn-small" href="{{ l.url }}" target="_blank" rel="noopener">{{ l.label }} ↗</a>
                  {% endfor %}
                </div>
              {% endif %}
            </div>
          </article>
        {% endfor %}

        {% if forloop.last and page.more_projects %}
          <div class="proj-more">
            <h3>More course projects</h3>
            <ul>
              {% for m in page.more_projects %}
                <li><span class="proj-more-name">{{ m.name }}</span><span class="proj-more-note">{{ m.note }}</span></li>
              {% endfor %}
            </ul>
          </div>
        {% endif %}
      </section>
    {% endif %}
  {% endfor %}
</div>
