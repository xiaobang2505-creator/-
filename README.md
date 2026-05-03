
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>AXIOM — 3D Product Visualist</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Barlow:ital,wght@0,100;0,200;0,300;0,400;0,500;1,100;1,200&family=Barlow+Condensed:wght@100;200;300&display=swap" rel="stylesheet">

<style>
/* ═══════════════════════════════ TOKENS ═══════════════════════════════ */
:root {
  --black:      #000000;
  --off-black:  #080808;
  --e-blue:     #00AEFF;
  --e-blue-dim: rgba(0,174,255,0.18);
  --e-blue-glow:rgba(0,174,255,0.06);
  --titanium:   #7A8A9A;
  --white:      #FFFFFF;
  --white-60:   rgba(255,255,255,0.6);
  --white-30:   rgba(255,255,255,0.3);
  --white-10:   rgba(255,255,255,0.1);
  --white-05:   rgba(255,255,255,0.05);
  --white-03:   rgba(255,255,255,0.03);
  --border:     rgba(255,255,255,0.06);
  --border-blue:rgba(0,174,255,0.22);
  --ease:       cubic-bezier(0.76,0,0.24,1);
  --ease-out:   cubic-bezier(0.16,1,0.3,1);
}

/* ═══════════════════════════════ RESET ═══════════════════════════════ */
*,*::before,*::after { box-sizing: border-box; margin: 0; padding: 0; }
html { font-size: 16px; overflow-x: hidden; }
body {
  font-family: 'Barlow', sans-serif;
  background: var(--black);
  color: var(--white);
  overflow-x: hidden;
  cursor: none;
}

/* ═══════════════════════════════ NOISE TEXTURE ═══════════════════════ */
body::before {
  content: '';
  position: fixed; inset: 0; z-index: 9998;
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.75' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)' opacity='0.03'/%3E%3C/svg%3E");
  pointer-events: none;
  opacity: 0.5;
}

/* ═══════════════════════════════ CURSOR ═══════════════════════════════ */
#cur-dot {
  position: fixed; z-index: 9999;
  width: 5px; height: 5px;
  background: var(--white);
  border-radius: 50%;
  pointer-events: none;
  transform: translate(-50%,-50%);
  transition: background 0.2s, width 0.2s, height 0.2s;
}
#cur-ring {
  position: fixed; z-index: 9997;
  width: 32px; height: 32px;
  border: 1px solid rgba(255,255,255,0.35);
  border-radius: 50%;
  pointer-events: none;
  transform: translate(-50%,-50%);
  transition: width 0.4s var(--ease), height 0.4s var(--ease), border-color 0.3s, background 0.3s;
}
body.link-hover #cur-dot { background: var(--e-blue); width: 7px; height: 7px; }
body.link-hover #cur-ring { width: 52px; height: 52px; border-color: var(--e-blue); background: var(--e-blue-glow); }

/* ═══════════════════════════════ PRELOADER ═══════════════════════════ */
#preloader {
  position: fixed; inset: 0; z-index: 9000;
  background: var(--black);
  display: flex; flex-direction: column; align-items: center; justify-content: center;
  transition: opacity 0.8s var(--ease), visibility 0.8s;
}
#preloader.done { opacity: 0; visibility: hidden; }
.pre-count {
  font-size: clamp(80px, 12vw, 140px);
  font-weight: 100;
  letter-spacing: -0.05em;
  line-height: 1;
  color: var(--white);
  font-variant-numeric: tabular-nums;
  transition: none;
}
.pre-label {
  font-size: 10px; font-weight: 300; letter-spacing: 0.5em;
  color: var(--titanium); text-transform: uppercase; margin-top: 2rem;
}
.pre-bar-wrap {
  position: absolute; bottom: 0; left: 0; right: 0;
  height: 2px; background: var(--white-05);
}
.pre-bar {
  height: 100%; background: var(--e-blue); width: 0%;
  transition: width 0.05s linear;
}

/* ═══════════════════════════════ NAV ═══════════════════════════════ */
nav {
  position: fixed; top: 0; left: 0; right: 0; z-index: 500;
  padding: 2rem 2.5rem;
  display: flex; align-items: center; justify-content: space-between;
  transition: padding 0.5s var(--ease), background 0.5s;
}
nav.scrolled {
  padding: 1.2rem 2.5rem;
  background: rgba(0,0,0,0.7);
  backdrop-filter: blur(20px);
  border-bottom: 1px solid var(--border);
}
.nav-logo {
  font-size: 13px; font-weight: 400; letter-spacing: 0.32em;
  text-transform: uppercase; color: var(--white); text-decoration: none;
}
.nav-logo sup {
  font-size: 8px; letter-spacing: 0.2em; color: var(--e-blue);
  vertical-align: super; margin-left: 2px;
}
.nav-links { display: flex; gap: 2.5rem; list-style: none; }
.nav-links a {
  font-size: 11px; font-weight: 300; letter-spacing: 0.18em;
  text-transform: uppercase; color: var(--white-60); text-decoration: none;
  transition: color 0.3s;
}
.nav-links a:hover { color: var(--white); }
.nav-cta {
  font-size: 10px; font-weight: 400; letter-spacing: 0.24em;
  text-transform: uppercase; color: var(--black);
  background: var(--white); text-decoration: none;
  padding: 0.7rem 1.6rem;
  transition: background 0.3s, color 0.3s;
  display: inline-block;
}
.nav-cta:hover { background: var(--e-blue); color: var(--black); }

/* ═══════════════════════════════ HERO CAROUSEL ═══════════════════════ */
#hero {
  position: relative; width: 100vw; height: 100vh;
  overflow: hidden; background: var(--black);
}
.hero-track {
  position: absolute; inset: 0;
}
.h-slide {
  position: absolute; inset: 0;
  opacity: 0;
  filter: blur(28px);
  transform: scale(1.05);
  transition: opacity 1.4s var(--ease), filter 1.4s var(--ease), transform 1.6s var(--ease);
  will-change: opacity, filter, transform;
}
.h-slide.active {
  opacity: 1; filter: blur(0px); transform: scale(1);
}
.h-slide.exiting {
  opacity: 0; filter: blur(20px); transform: scale(1.04);
  transition: opacity 1.0s var(--ease), filter 1.0s var(--ease), transform 1.2s var(--ease);
}
.h-slide-bg {
  position: absolute; inset: 0;
  display: flex; align-items: center; justify-content: center;
}
/* gradient backgrounds for slides */
.hbg-1 { background: radial-gradient(ellipse 70% 80% at 65% 40%, #0A1A2A 0%, #020508 55%, #000 100%); }
.hbg-2 { background: radial-gradient(ellipse 65% 75% at 35% 50%, #0A0E14 0%, #02040A 55%, #000 100%); }
.hbg-3 { background: radial-gradient(ellipse 60% 70% at 60% 45%, #100A0A 0%, #080303 55%, #000 100%); }

/* ambient glow behind product */
.h-glow {
  position: absolute;
  border-radius: 50%;
  filter: blur(80px);
  pointer-events: none;
}

/* Hero UI overlay */
.hero-ui {
  position: absolute; inset: 0; z-index: 20;
  display: flex; flex-direction: column; justify-content: flex-end;
  padding: 4rem 2.5rem;
  pointer-events: none;
}
.hero-ui-top {
  position: absolute; top: 2rem; left: 2.5rem; right: 2.5rem;
  display: flex; align-items: flex-start; justify-content: space-between;
  pointer-events: none;
}
.slide-index {
  font-size: 11px; font-weight: 200; letter-spacing: 0.35em;
  color: var(--white-30); text-transform: uppercase;
}
.slide-index span { color: var(--e-blue); font-weight: 400; }
.slide-cat {
  font-size: 10px; font-weight: 300; letter-spacing: 0.5em;
  color: var(--titanium); text-transform: uppercase;
  padding: 0.4rem 0.8rem;
  border: 1px solid var(--border);
}
.hero-bottom {
  display: flex; align-items: flex-end; justify-content: space-between;
}
.hero-text { pointer-events: all; }
.hero-eyebrow {
  font-size: 10px; font-weight: 300; letter-spacing: 0.5em;
  color: var(--e-blue); text-transform: uppercase; margin-bottom: 1.2rem;
  opacity: 0; transform: translateY(12px);
  animation: slideUp 0.8s var(--ease-out) 0.2s forwards;
}
.hero-title {
  font-family: 'Barlow Condensed', sans-serif;
  font-size: clamp(3.5rem, 8vw, 9rem);
  font-weight: 200; letter-spacing: -0.03em; line-height: 0.92;
  margin-bottom: 1.8rem;
  opacity: 0; transform: translateY(20px);
  animation: slideUp 1s var(--ease-out) 0.4s forwards;
}
.hero-title em { font-style: normal; color: var(--e-blue); }
.hero-sub {
  font-size: 13px; font-weight: 300; letter-spacing: 0.05em; line-height: 1.7;
  color: var(--white-60); max-width: 380px;
  opacity: 0; transform: translateY(12px);
  animation: slideUp 0.9s var(--ease-out) 0.6s forwards;
}
.hero-actions {
  display: flex; gap: 1rem; margin-top: 2.5rem;
  opacity: 0; animation: slideUp 0.8s var(--ease-out) 0.8s forwards;
}
.btn-view {
  padding: 0.85rem 2rem;
  font-size: 10px; font-weight: 400; letter-spacing: 0.3em; text-transform: uppercase;
  color: var(--black); background: var(--white); text-decoration: none;
  transition: background 0.3s, color 0.3s;
  display: inline-block;
}
.btn-view:hover { background: var(--e-blue); }
.btn-secondary {
  padding: 0.85rem 1.5rem;
  font-size: 10px; font-weight: 300; letter-spacing: 0.3em; text-transform: uppercase;
  color: var(--white-60); border: 1px solid var(--border);
  text-decoration: none; display: inline-flex; align-items: center; gap: 0.6rem;
  transition: color 0.3s, border-color 0.3s;
}
.btn-secondary:hover { color: var(--white); border-color: var(--white-30); }

/* slide nav */
.slide-nav {
  display: flex; flex-direction: column; gap: 8px; align-items: flex-end;
  pointer-events: all;
}
.slide-dot {
  width: 24px; height: 1px; background: var(--white-30);
  cursor: none; transition: background 0.3s, width 0.4s var(--ease);
}
.slide-dot.active { background: var(--e-blue); width: 40px; }

/* progress bar */
.hero-progress {
  position: absolute; bottom: 0; left: 0; right: 0; height: 1px;
  background: var(--white-05); z-index: 25;
}
.hero-progress-bar {
  height: 100%; background: var(--e-blue); width: 0%;
  transition: width 5s linear;
}
.hero-progress-bar.running { width: 100%; }
.hero-progress-bar.reset { transition: none; width: 0%; }

/* ═══════════════════════════════ MARQUEE ═══════════════════════════ */
.marquee-strip {
  border-top: 1px solid var(--border);
  border-bottom: 1px solid var(--border);
  overflow: hidden; padding: 1rem 0;
  background: var(--off-black);
}
.marquee-inner {
  display: flex; animation: marq 30s linear infinite; white-space: nowrap;
}
.marquee-inner:hover { animation-play-state: paused; }
.mi-item {
  display: inline-flex; align-items: center; gap: 2rem; padding: 0 2rem;
  font-size: 10px; font-weight: 300; letter-spacing: 0.4em;
  color: var(--titanium); text-transform: uppercase;
}
.mi-dot { width: 3px; height: 3px; background: var(--e-blue); border-radius: 50%; flex-shrink: 0; }

/* ═══════════════════════════════ SECTION SHARED ═══════════════════════ */
.sec-label {
  font-size: 10px; font-weight: 300; letter-spacing: 0.5em;
  color: var(--e-blue); text-transform: uppercase; margin-bottom: 0.5rem;
}
.sec-title {
  font-family: 'Barlow Condensed', sans-serif;
  font-weight: 200; letter-spacing: -0.02em;
  font-size: clamp(2.2rem, 4.5vw, 4rem); line-height: 1.05;
}
.reveal {
  opacity: 0; transform: translateY(28px);
  transition: opacity 0.9s var(--ease-out), transform 0.9s var(--ease-out);
}
.reveal.vis { opacity: 1; transform: translateY(0); }
.reveal.d1 { transition-delay: 0.1s; }
.reveal.d2 { transition-delay: 0.2s; }
.reveal.d3 { transition-delay: 0.3s; }
.reveal.d4 { transition-delay: 0.4s; }

/* ═══════════════════════════════ WORKS / BENTO ═══════════════════════ */
#works { padding: 8rem 2.5rem; }
.works-header {
  display: flex; align-items: flex-end; justify-content: space-between;
  margin-bottom: 4rem; padding-bottom: 2rem;
  border-bottom: 1px solid var(--border);
}
.works-count {
  font-family: 'Barlow Condensed', sans-serif;
  font-size: 7rem; font-weight: 100; letter-spacing: -0.04em;
  color: var(--white-05); line-height: 1; align-self: flex-start;
  margin-left: auto; padding-left: 2rem;
}

/* BENTO GRID */
.bento {
  display: grid;
  grid-template-columns: 1.8fr 1fr 1fr;
  grid-template-rows: 380px 280px;
  gap: 2px;
}
.bento-card {
  position: relative; overflow: hidden;
  background: var(--off-black); cursor: none;
}
.bento-card:nth-child(1) { grid-row: span 2; }
.bento-card:nth-child(4) { grid-column: 2 / span 2; }

/* DOF lazy load */
.bc-img {
  position: absolute; inset: 0;
  filter: blur(20px); transform: scale(1.05);
  transition: filter 1.6s var(--ease-out), transform 0.8s var(--ease);
}
.bento-card.loaded .bc-img { filter: blur(0); transform: scale(1); }
.bento-card:hover .bc-img { transform: scale(1.04) !important; }

/* glassmorphism hover */
.bc-glass {
  position: absolute; inset: 0; z-index: 5;
  background: linear-gradient(135deg, rgba(0,174,255,0.07) 0%, rgba(0,0,0,0.5) 100%);
  border: 1px solid rgba(0,174,255,0);
  backdrop-filter: blur(0px);
  opacity: 0;
  transition: opacity 0.5s, border-color 0.5s, backdrop-filter 0.5s;
}
.bento-card:hover .bc-glass {
  opacity: 1;
  border-color: var(--border-blue);
  backdrop-filter: blur(4px);
}

/* card meta overlay */
.bc-meta {
  position: absolute; inset: 0; z-index: 10;
  display: flex; flex-direction: column; justify-content: flex-end;
  padding: 2rem;
  background: linear-gradient(to top, rgba(0,0,0,0.8) 0%, transparent 55%);
}
.bc-tag {
  font-size: 9px; font-weight: 300; letter-spacing: 0.45em;
  color: var(--e-blue); text-transform: uppercase; margin-bottom: 0.5rem;
}
.bc-name {
  font-family: 'Barlow Condensed', sans-serif;
  font-size: clamp(1.2rem, 2.5vw, 1.8rem);
  font-weight: 200; letter-spacing: 0.01em; line-height: 1.1;
}
.bc-specs {
  margin-top: 1rem;
  display: flex; gap: 1.5rem;
  opacity: 0; transform: translateY(8px);
  transition: opacity 0.4s, transform 0.4s var(--ease);
}
.bento-card:hover .bc-specs { opacity: 1; transform: translateY(0); }
.bc-spec {
  font-size: 9px; font-weight: 300; letter-spacing: 0.25em;
  color: var(--titanium); text-transform: uppercase;
}
.bc-spec span { color: var(--white-60); display: block; margin-top: 2px; }
.bc-arrow {
  position: absolute; top: 1.5rem; right: 1.5rem; z-index: 15;
  width: 36px; height: 36px;
  border: 1px solid rgba(0,174,255,0);
  display: flex; align-items: center; justify-content: center;
  transform: scale(0) rotate(-45deg);
  opacity: 0;
  transition: transform 0.4s var(--ease), opacity 0.4s, border-color 0.3s, background 0.3s;
}
.bento-card:hover .bc-arrow {
  transform: scale(1) rotate(0deg); opacity: 1;
  border-color: var(--border-blue);
}
.bc-arrow:hover { background: var(--e-blue) !important; border-color: var(--e-blue) !important; }
.bc-arrow svg { width: 12px; height: 12px; stroke: var(--white); fill: none; stroke-width: 1.5; }

/* card backgrounds / synthetic renders */
.render-1 {
  width: 100%; height: 100%;
  background:
    radial-gradient(ellipse 50% 65% at 60% 35%, rgba(0,80,160,0.25) 0%, transparent 55%),
    radial-gradient(ellipse 35% 40% at 25% 75%, rgba(0,40,80,0.15) 0%, transparent 50%),
    linear-gradient(160deg, #040A14 0%, #060D1A 45%, #020608 100%);
}
.render-2 {
  width: 100%; height: 100%;
  background:
    radial-gradient(ellipse 55% 55% at 50% 40%, rgba(0,60,120,0.2) 0%, transparent 55%),
    radial-gradient(ellipse 40% 35% at 80% 80%, rgba(0,174,255,0.06) 0%, transparent 50%),
    linear-gradient(145deg, #050810 0%, #080C18 55%, #030510 100%);
}
.render-3 {
  width: 100%; height: 100%;
  background:
    radial-gradient(ellipse 50% 60% at 40% 35%, rgba(0,50,100,0.22) 0%, transparent 55%),
    linear-gradient(150deg, #040810 0%, #060A14 50%, #020506 100%);
}
.render-4 {
  width: 100%; height: 100%;
  background:
    radial-gradient(ellipse 60% 60% at 55% 45%, rgba(0,70,140,0.2) 0%, transparent 55%),
    radial-gradient(ellipse 35% 35% at 15% 80%, rgba(0,174,255,0.05) 0%, transparent 50%),
    linear-gradient(155deg, #04090E 0%, #070C15 50%, #020408 100%);
}
.render-5 {
  width: 100%; height: 100%;
  background:
    radial-gradient(ellipse 55% 50% at 60% 40%, rgba(0,60,120,0.18) 0%, transparent 55%),
    linear-gradient(140deg, #050710 0%, #08090E 50%, #030408 100%);
}
/* floating product SVG in card */
.bc-product { position: absolute; inset: 0; display: flex; align-items: center; justify-content: center; z-index: 1; }

/* ═══════════════════════════════ FLOATING NAV ═══════════════════════ */
#float-nav {
  position: fixed; right: 2rem; top: 50%;
  transform: translateY(-50%);
  z-index: 400;
  display: flex; flex-direction: column; gap: 1.2rem; align-items: flex-end;
}
.fn-item {
  display: flex; align-items: center; gap: 0.8rem; cursor: none;
}
.fn-label {
  font-size: 9px; font-weight: 300; letter-spacing: 0.3em;
  color: var(--titanium); text-transform: uppercase;
  opacity: 0; transform: translateX(6px);
  transition: opacity 0.3s, transform 0.3s, color 0.3s;
}
.fn-item:hover .fn-label { opacity: 1; transform: translateX(0); color: var(--white); }
.fn-dot {
  width: 4px; height: 4px; border-radius: 50%;
  background: var(--white-30); flex-shrink: 0;
  transition: background 0.3s, transform 0.3s, width 0.3s;
}
.fn-item:hover .fn-dot { background: var(--white); transform: scale(1.5); }
.fn-item.active .fn-dot { background: var(--e-blue); width: 14px; border-radius: 2px; }

/* ═══════════════════════════════ ABOUT ═══════════════════════════════ */
#about {
  padding: 8rem 2.5rem;
  display: grid; grid-template-columns: 1fr 1fr;
  gap: 6rem; align-items: center;
  border-top: 1px solid var(--border);
}
.about-line {
  display: flex; align-items: center; gap: 2rem; margin-bottom: 3rem;
}
.about-line::before, .about-line::after {
  content: ''; flex: 1; height: 1px; background: var(--border);
}
.about-line::after { width: 40%; flex: none; }
.about-line-text {
  font-size: 9px; font-weight: 300; letter-spacing: 0.5em;
  color: var(--titanium); text-transform: uppercase; white-space: nowrap;
}
.about-left .sec-title { margin-bottom: 2rem; }
.about-desc {
  font-size: 14px; font-weight: 300; line-height: 1.9; letter-spacing: 0.03em;
  color: var(--white-60); margin-bottom: 1.4rem;
}
.about-stats {
  display: grid; grid-template-columns: 1fr 1fr; gap: 2rem;
  margin-top: 3rem; padding-top: 3rem; border-top: 1px solid var(--border);
}
.a-stat-n {
  font-family: 'Barlow Condensed', sans-serif;
  font-size: 3.5rem; font-weight: 100; letter-spacing: -0.03em;
  color: var(--white); line-height: 1; margin-bottom: 4px;
}
.a-stat-n em { font-style: normal; color: var(--e-blue); font-size: 1.5rem; }
.a-stat-l {
  font-size: 10px; font-weight: 300; letter-spacing: 0.28em;
  color: var(--titanium); text-transform: uppercase;
}
.about-right { position: relative; }
.about-render-wrap {
  position: relative;
  width: 100%; aspect-ratio: 3/4;
  background: linear-gradient(145deg, #050810 0%, #080D18 55%, #030508 100%);
  overflow: hidden;
}
.about-render-wrap::before {
  content: '';
  position: absolute; inset: 0;
  background: radial-gradient(ellipse 55% 65% at 60% 35%, rgba(0,80,160,0.22) 0%, transparent 55%);
}
.about-accent {
  position: absolute; bottom: -2px; left: 0;
  width: 40%; height: 40%;
  background: linear-gradient(135deg, #020409 0%, #04070F 100%);
  border: 1px solid var(--border);
  display: flex; align-items: center; justify-content: center;
}
.about-accent-text {
  font-family: 'Barlow Condensed', sans-serif;
  font-size: 0.75rem; font-weight: 200; letter-spacing: 0.3em;
  color: var(--e-blue); text-transform: uppercase; text-align: center; line-height: 1.6;
}

/* ═══════════════════════════════ CASE STUDY ═══════════════════════════ */
#case { padding: 8rem 0; border-top: 1px solid var(--border); }
.case-header {
  padding: 0 2.5rem 5rem;
  display: grid; grid-template-columns: 1fr 1fr;
  gap: 4rem; align-items: end;
}
.case-h-left { }
.case-h-right {
  font-size: 13px; font-weight: 300; line-height: 1.9;
  letter-spacing: 0.03em; color: var(--white-60);
}
.case-split {
  display: grid; grid-template-columns: 2fr 3fr;
  gap: 0; border-top: 1px solid var(--border);
}
.case-text-col {
  padding: 5rem 2.5rem;
  border-right: 1px solid var(--border);
  position: sticky; top: 120px;
  height: fit-content;
}
.case-chapter {
  margin-bottom: 4rem; padding-bottom: 4rem;
  border-bottom: 1px solid var(--border);
}
.case-chapter:last-child { border-bottom: none; margin-bottom: 0; }
.chapter-num {
  font-size: 9px; font-weight: 300; letter-spacing: 0.5em;
  color: var(--e-blue); text-transform: uppercase; margin-bottom: 1rem;
}
.chapter-title {
  font-family: 'Barlow Condensed', sans-serif;
  font-size: 1.6rem; font-weight: 200; letter-spacing: 0.02em;
  margin-bottom: 1.2rem; line-height: 1.15;
}
.chapter-body {
  font-size: 12.5px; font-weight: 300; line-height: 1.9;
  letter-spacing: 0.03em; color: var(--white-60);
}
.case-spec-table { margin-top: 2rem; }
.cst-row {
  display: flex; justify-content: space-between;
  padding: 0.7rem 0; border-bottom: 1px solid var(--border);
  font-size: 11px;
}
.cst-row:last-child { border-bottom: none; }
.cst-k { font-weight: 300; letter-spacing: 0.15em; color: var(--titanium); text-transform: uppercase; font-size: 10px; }
.cst-v { font-weight: 300; color: var(--white-60); }

.case-img-col { padding: 0; }
.ci-block {
  position: relative; overflow: hidden;
  border-bottom: 1px solid var(--border);
}
.ci-inner {
  width: 100%; height: 100%; min-height: 500px;
  filter: blur(14px); transform: scale(1.04);
  transition: filter 1.4s var(--ease-out), transform 0.8s var(--ease);
  display: flex; align-items: center; justify-content: center;
}
.ci-block.loaded .ci-inner { filter: blur(0); transform: scale(1); }
.ci-block:hover .ci-inner { transform: scale(1.02); }
.ci-caption {
  position: absolute; bottom: 1.5rem; left: 2rem; right: 2rem;
  display: flex; justify-content: space-between; align-items: center;
}
.ci-cap-label {
  font-size: 9px; font-weight: 300; letter-spacing: 0.4em;
  color: rgba(255,255,255,0.35); text-transform: uppercase;
}
.ci-cap-num {
  font-family: 'Barlow Condensed', sans-serif;
  font-size: 2.5rem; font-weight: 100; letter-spacing: -0.02em;
  color: rgba(255,255,255,0.06);
}

/* render backgrounds for case study */
.cir-1 {
  width: 100%; min-height: 500px;
  background:
    radial-gradient(ellipse 55% 65% at 60% 35%, rgba(0,80,170,0.28) 0%, transparent 55%),
    radial-gradient(ellipse 30% 35% at 20% 80%, rgba(0,174,255,0.06) 0%, transparent 50%),
    linear-gradient(150deg, #030914 0%, #060E1F 50%, #020608 100%);
}
.cir-2 {
  width: 100%; min-height: 380px;
  background:
    radial-gradient(ellipse 60% 50% at 30% 40%, rgba(0,174,255,0.1) 0%, transparent 50%),
    linear-gradient(160deg, #040A14 0%, #060D1C 55%, #030610 100%);
}
.cir-3 {
  width: 100%; min-height: 500px;
  background:
    radial-gradient(ellipse 50% 60% at 65% 40%, rgba(0,70,160,0.22) 0%, transparent 55%),
    radial-gradient(ellipse 35% 30% at 10% 70%, rgba(0,100,200,0.08) 0%, transparent 50%),
    linear-gradient(155deg, #030810 0%, #050B18 55%, #020508 100%);
}

/* ═══════════════════════════════ CAPABILITIES ════════════════════════ */
#caps { padding: 8rem 2.5rem; border-top: 1px solid var(--border); }
.caps-grid {
  display: grid; grid-template-columns: repeat(4, 1fr);
  gap: 2px; margin-top: 5rem;
}
.cap-card {
  padding: 3rem 2rem;
  background: var(--white-03);
  border: 1px solid var(--border);
  position: relative; overflow: hidden;
  transition: background 0.4s, border-color 0.4s;
}
.cap-card::before {
  content: '';
  position: absolute; top: 0; left: 0; right: 0; height: 1px;
  background: linear-gradient(90deg, transparent, var(--e-blue), transparent);
  opacity: 0; transition: opacity 0.4s;
}
.cap-card:hover { background: var(--e-blue-glow); border-color: var(--border-blue); }
.cap-card:hover::before { opacity: 1; }
.cap-num {
  font-family: 'Barlow Condensed', sans-serif;
  font-size: 4rem; font-weight: 100; letter-spacing: -0.04em;
  color: var(--white-05); line-height: 1; margin-bottom: 2rem;
  transition: color 0.4s;
}
.cap-card:hover .cap-num { color: rgba(0,174,255,0.12); }
.cap-title {
  font-size: 14px; font-weight: 300; letter-spacing: 0.05em;
  margin-bottom: 1rem; line-height: 1.3;
}
.cap-desc {
  font-size: 12px; font-weight: 300; line-height: 1.8;
  letter-spacing: 0.03em; color: var(--white-60);
}

/* ═══════════════════════════════ CONTACT ═══════════════════════════════ */
#contact {
  padding: 10rem 2.5rem;
  text-align: center; position: relative; overflow: hidden;
  border-top: 1px solid var(--border);
}
.contact-bg {
  position: absolute; top: 50%; left: 50%;
  transform: translate(-50%,-50%);
  width: 800px; height: 400px;
  background: radial-gradient(ellipse, rgba(0,174,255,0.05) 0%, transparent 70%);
  pointer-events: none;
}
.contact-grid {
  position: absolute; inset: 0; z-index: 0;
  background-image:
    linear-gradient(var(--white-03) 1px, transparent 1px),
    linear-gradient(90deg, var(--white-03) 1px, transparent 1px);
  background-size: 60px 60px;
  mask-image: radial-gradient(ellipse 60% 80% at 50% 50%, black 30%, transparent 100%);
}
.contact-inner { position: relative; z-index: 5; max-width: 800px; margin: 0 auto; }
.contact-kicker {
  font-size: 10px; font-weight: 300; letter-spacing: 0.5em;
  color: var(--e-blue); text-transform: uppercase; margin-bottom: 2rem;
}
.contact-title {
  font-family: 'Barlow Condensed', sans-serif;
  font-size: clamp(3rem, 7vw, 7rem);
  font-weight: 100; letter-spacing: -0.03em; line-height: 0.95;
  margin-bottom: 2.5rem;
}
.contact-sub {
  font-size: 14px; font-weight: 300; line-height: 1.8;
  letter-spacing: 0.04em; color: var(--white-60); margin-bottom: 4rem;
}
.contact-email {
  display: inline-block;
  font-family: 'Barlow Condensed', sans-serif;
  font-size: clamp(1.5rem, 3vw, 2.5rem);
  font-weight: 200; letter-spacing: 0.05em;
  color: var(--white); text-decoration: none;
  border-bottom: 1px solid var(--border);
  padding-bottom: 6px;
  transition: color 0.3s, border-color 0.3s;
}
.contact-email:hover { color: var(--e-blue); border-color: var(--e-blue); }
.contact-links { display: flex; justify-content: center; gap: 2rem; margin-top: 4rem; }
.c-link {
  font-size: 10px; font-weight: 300; letter-spacing: 0.3em;
  color: var(--titanium); text-decoration: none; text-transform: uppercase;
  transition: color 0.3s;
}
.c-link:hover { color: var(--white); }

/* ═══════════════════════════════ FOOTER ═══════════════════════════════ */
footer {
  padding: 2rem 2.5rem;
  border-top: 1px solid var(--border);
  display: flex; align-items: center; justify-content: space-between;
}
.f-logo { font-size: 12px; font-weight: 400; letter-spacing: 0.32em; color: var(--white-30); text-transform: uppercase; }
.f-copy { font-size: 10px; font-weight: 300; letter-spacing: 0.2em; color: var(--white-30); }
.f-soc { display: flex; gap: 2rem; }
.f-soc a { font-size: 10px; font-weight: 300; letter-spacing: 0.25em; color: var(--white-30); text-decoration: none; text-transform: uppercase; transition: color 0.3s; }
.f-soc a:hover { color: var(--e-blue); }

/* ═══════════════════════════════ ANIMATIONS ═══════════════════════════ */
@keyframes slideUp {
  to { opacity: 1; transform: translateY(0); }
}
@keyframes marq {
  from { transform: translateX(0); }
  to { transform: translateX(-50%); }
}
@keyframes pulse-dot {
  0%,100% { opacity: 0.4; }
  50% { opacity: 1; }
}
</style>
</head>
<body>

<div id="cur-dot"></div>
<div id="cur-ring"></div>

<!-- ═══ PRELOADER ═══ -->
<div id="preloader">
  <div class="pre-count" id="pre-num">000</div>
  <div class="pre-label">Initializing render engine</div>
  <div class="pre-bar-wrap"><div class="pre-bar" id="pre-bar"></div></div>
</div>

<!-- ═══ NAV ═══ -->
<nav id="nav">
  <a href="#" class="nav-logo">AXIOM<sup>®</sup></a>
  <ul class="nav-links">
    <li><a href="#works">Work</a></li>
    <li><a href="#case">Case Studies</a></li>
    <li><a href="#caps">Services</a></li>
    <li><a href="#about">Studio</a></li>
  </ul>
  <a href="#contact" class="nav-cta">Commission Work</a>
</nav>

<!-- ═══ HERO CAROUSEL ═══ -->
<section id="hero">
  <div class="hero-track" id="heroTrack">

    <!-- SLIDE 1: FRAGRANCE -->
    <div class="h-slide active" data-cat="Fragrance · KV 001">
      <div class="h-slide-bg hbg-1">
        <!-- ambient glow -->
        <div class="h-glow" style="width:500px;height:500px;background:radial-gradient(circle,rgba(0,120,255,0.14) 0%,transparent 70%);top:20%;right:15%;"></div>
        <div class="h-glow" style="width:300px;height:300px;background:radial-gradient(circle,rgba(0,174,255,0.07) 0%,transparent 70%);bottom:20%;left:30%;"></div>
        <!-- product -->
        <div style="position:relative;width:min(380px,40vw);height:min(560px,55vh);">
          <svg width="100%" height="100%" viewBox="0 0 360 540" fill="none" xmlns="http://www.w3.org/2000/svg">
            <defs>
              <linearGradient id="bottle-body" x1="0%" y1="0%" x2="100%" y2="0%">
                <stop offset="0%" stop-color="#030B18" stop-opacity="0.98"/>
                <stop offset="18%" stop-color="#0A1A30" stop-opacity="0.9"/>
                <stop offset="50%" stop-color="#1A3A6A" stop-opacity="0.25"/>
                <stop offset="72%" stop-color="#0A1A30" stop-opacity="0.85"/>
                <stop offset="100%" stop-color="#040C1E" stop-opacity="0.98"/>
              </linearGradient>
              <linearGradient id="bottle-liquid" x1="0%" y1="0%" x2="100%" y2="0%">
                <stop offset="0%" stop-color="#020810"/>
                <stop offset="30%" stop-color="#0A1A2E"/>
                <stop offset="50%" stop-color="#152A50"/>
                <stop offset="70%" stop-color="#0A1A2E"/>
                <stop offset="100%" stop-color="#020810"/>
              </linearGradient>
              <radialGradient id="rim-shine" cx="50%" cy="0%" r="100%">
                <stop offset="0%" stop-color="#00AEFF" stop-opacity="0.9"/>
                <stop offset="60%" stop-color="#0066AA" stop-opacity="0.4"/>
                <stop offset="100%" stop-color="#001A33" stop-opacity="0.1"/>
              </radialGradient>
              <linearGradient id="cap-grad" x1="0%" y1="0%" x2="100%" y2="0%">
                <stop offset="0%" stop-color="#080E18"/>
                <stop offset="25%" stop-color="#1A2840"/>
                <stop offset="50%" stop-color="#2A4060"/>
                <stop offset="75%" stop-color="#1A2840"/>
                <stop offset="100%" stop-color="#080E18"/>
              </linearGradient>
              <linearGradient id="cap-top" x1="0%" y1="0%" x2="100%" y2="100%">
                <stop offset="0%" stop-color="#2A4060"/>
                <stop offset="100%" stop-color="#050A14"/>
              </linearGradient>
              <filter id="blur-sm"><feGaussianBlur stdDeviation="2"/></filter>
              <filter id="glow-e">
                <feGaussianBlur stdDeviation="6" result="blur"/>
                <feComposite in="SourceGraphic" in2="blur" operator="over"/>
              </filter>
            </defs>
            <!-- shadow -->
            <ellipse cx="180" cy="528" rx="88" ry="10" fill="#00AEFF" opacity="0.04" filter="url(#blur-sm)"/>
            <ellipse cx="180" cy="530" rx="60" ry="6" fill="black" opacity="0.6"/>
            <!-- bottle body -->
            <rect x="80" y="160" width="200" height="320" rx="4" fill="#06101E" opacity="0.98"/>
            <!-- liquid fill inside (lighter) -->
            <rect x="82" y="162" width="196" height="316" rx="3" fill="url(#bottle-liquid)" opacity="0.85"/>
            <!-- side face shading -->
            <rect x="80" y="160" width="200" height="320" rx="4" fill="url(#bottle-body)" opacity="0.9"/>
            <!-- subtle inner glow (backlight through glass) -->
            <rect x="82" y="162" width="196" height="316" rx="3" fill="none"/>
            <ellipse cx="180" cy="320" rx="60" ry="120" fill="#002244" opacity="0.25"/>
            <!-- edge highlight left -->
            <rect x="80" y="162" width="3" height="316" rx="1.5" fill="#00AEFF" opacity="0.18" filter="url(#glow-e)"/>
            <!-- edge highlight right -->
            <rect x="277" y="162" width="3" height="316" rx="1.5" fill="#00AEFF" opacity="0.1"/>
            <!-- specular streak -->
            <rect x="100" y="180" width="12" height="260" rx="6" fill="white" opacity="0.022"/>
            <rect x="116" y="175" width="4" height="100" rx="2" fill="white" opacity="0.028"/>
            <!-- caustic light bands (refraction pattern) -->
            <path d="M90 240 Q180 220 270 248" stroke="rgba(0,174,255,0.12)" stroke-width="1" fill="none"/>
            <path d="M88 255 Q180 238 272 262" stroke="rgba(0,174,255,0.08)" stroke-width="0.5" fill="none"/>
            <path d="M90 280 Q180 266 270 285" stroke="rgba(0,174,255,0.06)" stroke-width="0.5" fill="none"/>
            <!-- label zone lines -->
            <rect x="90" y="290" width="180" height="0.5" fill="rgba(0,174,255,0.25)"/>
            <rect x="90" y="370" width="180" height="0.5" fill="rgba(0,174,255,0.15)"/>
            <!-- top rim -->
            <rect x="80" y="156" width="200" height="8" rx="2" fill="url(#rim-shine)" opacity="0.85" filter="url(#glow-e)"/>
            <rect x="80" y="476" width="200" height="4" rx="2" fill="rgba(0,80,160,0.5)"/>
            <!-- neck -->
            <rect x="128" y="110" width="104" height="52" rx="3" fill="#04090E"/>
            <rect x="128" y="110" width="104" height="52" rx="3" fill="url(#bottle-body)" opacity="0.7"/>
            <rect x="128" y="108" width="104" height="6" rx="2" fill="url(#rim-shine)" opacity="0.7"/>
            <rect x="130" y="112" width="6" height="46" rx="3" fill="white" opacity="0.015"/>
            <!-- cap (top section) -->
            <rect x="110" y="42" width="140" height="72" rx="5" fill="url(#cap-grad)"/>
            <!-- cap top face -->
            <path d="M110 42 L250 42 L250 48 Q180 38 110 48 Z" fill="url(#cap-top)" opacity="0.6"/>
            <!-- cap edge lines (machined look) -->
            <rect x="110" y="68" width="140" height="1" fill="rgba(0,174,255,0.2)"/>
            <rect x="110" y="92" width="140" height="0.5" fill="rgba(0,174,255,0.15)"/>
            <!-- cap specular -->
            <rect x="116" y="46" width="14" height="60" rx="7" fill="white" opacity="0.04"/>
            <rect x="110" y="40" width="140" height="5" rx="2" fill="rgba(0,174,255,0.5)" filter="url(#glow-e)" opacity="0.6"/>
            <!-- rim light at cap bottom -->
            <rect x="110" y="110" width="140" height="4" rx="2" fill="url(#rim-shine)" opacity="0.55"/>
            <!-- brand text simulation -->
            <rect x="128" y="308" width="104" height="1" fill="rgba(0,174,255,0.4)"/>
            <rect x="138" y="320" width="84" height="0.4" fill="rgba(0,174,255,0.25)"/>
            <rect x="142" y="334" width="76" height="0.4" fill="rgba(0,174,255,0.18)"/>
            <!-- ambient particles -->
            <circle cx="310" cy="200" r="1.5" fill="#00AEFF" opacity="0.5"/>
            <circle cx="330" cy="270" r="1" fill="#00AEFF" opacity="0.35"/>
            <circle cx="55" cy="320" r="1.5" fill="#00AEFF" opacity="0.3"/>
            <circle cx="40" cy="400" r="1" fill="white" opacity="0.15"/>
            <circle cx="325" cy="370" r="2" fill="#00AEFF" opacity="0.25"/>
          </svg>
        </div>
      </div>
    </div>

    <!-- SLIDE 2: TECH -->
    <div class="h-slide" data-cat="Technology · KV 002">
      <div class="h-slide-bg hbg-2">
        <div class="h-glow" style="width:600px;height:350px;background:radial-gradient(ellipse,rgba(0,100,200,0.12) 0%,transparent 70%);top:25%;left:10%;"></div>
        <div class="h-glow" style="width:250px;height:250px;background:radial-gradient(circle,rgba(0,174,255,0.08) 0%,transparent 70%);bottom:15%;right:20%;"></div>
        <div style="position:relative;width:min(440px,45vw);height:min(440px,44vh);">
          <svg width="100%" height="100%" viewBox="0 0 440 380" fill="none">
            <defs>
              <linearGradient id="hph-shell" x1="0%" y1="0%" x2="100%" y2="100%">
                <stop offset="0%" stop-color="#0E1520"/>
                <stop offset="45%" stop-color="#1A2840"/>
                <stop offset="100%" stop-color="#070C14"/>
              </linearGradient>
              <radialGradient id="hph-reflect" cx="30%" cy="25%" r="70%">
                <stop offset="0%" stop-color="rgba(0,174,255,0.2)"/>
                <stop offset="100%" stop-color="transparent"/>
              </radialGradient>
              <linearGradient id="arc-grad" x1="0%" y1="0%" x2="100%" y2="0%">
                <stop offset="0%" stop-color="#04090E"/>
                <stop offset="40%" stop-color="#0E1A28"/>
                <stop offset="60%" stop-color="#162438"/>
                <stop offset="100%" stop-color="#04090E"/>
              </linearGradient>
            </defs>
            <!-- left cup -->
            <ellipse cx="120" cy="220" rx="88" ry="96" fill="url(#hph-shell)"/>
            <ellipse cx="120" cy="220" rx="88" ry="96" fill="url(#hph-reflect)" opacity="0.6"/>
            <ellipse cx="95" cy="200" rx="40" ry="30" fill="rgba(0,174,255,0.04)"/>
            <!-- left cup driver -->
            <ellipse cx="120" cy="220" rx="62" ry="68" fill="#050A12"/>
            <ellipse cx="120" cy="220" rx="48" ry="54" fill="#040810" stroke="rgba(0,174,255,0.15)" stroke-width="0.5"/>
            <ellipse cx="120" cy="220" rx="30" ry="34" fill="#030609"/>
            <!-- left cup mesh pattern -->
            <ellipse cx="120" cy="220" rx="18" ry="20" fill="#020407" stroke="rgba(0,174,255,0.2)" stroke-width="0.5"/>
            <!-- left cup rim highlight -->
            <path d="M58 168 Q32 220 58 272" stroke="rgba(0,174,255,0.3)" stroke-width="1.5" fill="none" stroke-linecap="round"/>
            <path d="M182 165 Q208 220 182 275" stroke="rgba(255,255,255,0.04)" stroke-width="1" fill="none"/>
            <!-- LED indicator -->
            <circle cx="90" cy="175" r="3" fill="#00AEFF" opacity="0.9"/>
            <circle cx="90" cy="175" r="7" fill="#00AEFF" opacity="0.15"/>
            <!-- arc / headband -->
            <path d="M160 168 Q220 50 280 168" stroke="url(#arc-grad)" stroke-width="28" fill="none" stroke-linecap="round"/>
            <path d="M160 168 Q220 50 280 168" stroke="rgba(0,174,255,0.08)" stroke-width="28" fill="none" stroke-linecap="round"/>
            <!-- headband top highlight -->
            <path d="M165 160 Q220 46 275 160" stroke="rgba(255,255,255,0.05)" stroke-width="4" fill="none" stroke-linecap="round"/>
            <path d="M168 156 Q220 50 272 156" stroke="rgba(0,174,255,0.15)" stroke-width="1" fill="none" stroke-linecap="round"/>
            <!-- headband inner -->
            <path d="M162 175 Q220 60 278 175" stroke="#030609" stroke-width="20" fill="none" stroke-linecap="round"/>
            <!-- right cup -->
            <ellipse cx="320" cy="220" rx="88" ry="96" fill="url(#hph-shell)"/>
            <ellipse cx="320" cy="220" rx="88" ry="96" fill="url(#hph-reflect)" opacity="0.4"/>
            <ellipse cx="320" cy="220" rx="62" ry="68" fill="#050A12"/>
            <ellipse cx="320" cy="220" rx="48" ry="54" fill="#040810" stroke="rgba(0,174,255,0.1)" stroke-width="0.5"/>
            <ellipse cx="320" cy="220" rx="30" ry="34" fill="#030609"/>
            <ellipse cx="320" cy="220" rx="18" ry="20" fill="#020407" stroke="rgba(0,174,255,0.18)" stroke-width="0.5"/>
            <!-- right cup rim highlight -->
            <path d="M382 168 Q408 220 382 272" stroke="rgba(0,174,255,0.18)" stroke-width="1.5" fill="none" stroke-linecap="round"/>
            <!-- specular on right -->
            <ellipse cx="295" cy="195" rx="18" ry="12" fill="rgba(255,255,255,0.025)"/>
            <!-- shadow -->
            <ellipse cx="220" cy="348" rx="160" ry="18" fill="rgba(0,60,140,0.12)"/>
            <!-- ambient particles -->
            <circle cx="50" cy="150" r="1.5" fill="#00AEFF" opacity="0.4"/>
            <circle cx="390" cy="290" r="1" fill="#00AEFF" opacity="0.35"/>
            <circle cx="220" cy="30" r="1" fill="white" opacity="0.2"/>
          </svg>
        </div>
      </div>
    </div>

    <!-- SLIDE 3: SKINCARE -->
    <div class="h-slide" data-cat="Skincare · KV 003">
      <div class="h-slide-bg hbg-3">
        <div class="h-glow" style="width:400px;height:600px;background:radial-gradient(ellipse,rgba(0,80,180,0.16) 0%,transparent 70%);top:10%;right:25%;"></div>
        <div class="h-glow" style="width:200px;height:200px;background:radial-gradient(circle,rgba(0,174,255,0.06) 0%,transparent 70%);bottom:25%;left:20%;"></div>
        <div style="position:relative;width:min(280px,30vw);height:min(560px,55vh);">
          <svg width="100%" height="100%" viewBox="0 0 280 560" fill="none">
            <defs>
              <linearGradient id="sk-body" x1="0%" y1="0%" x2="100%" y2="0%">
                <stop offset="0%" stop-color="#040A12"/>
                <stop offset="20%" stop-color="#0A1828"/>
                <stop offset="50%" stop-color="#102030" stop-opacity="0.6"/>
                <stop offset="80%" stop-color="#0A1828"/>
                <stop offset="100%" stop-color="#040A12"/>
              </linearGradient>
              <linearGradient id="sk-frosted" x1="0%" y1="0%" x2="100%" y2="0%">
                <stop offset="0%" stop-color="#040A12"/>
                <stop offset="35%" stop-color="#0E1E30" stop-opacity="0.7"/>
                <stop offset="50%" stop-color="#1A3050" stop-opacity="0.4"/>
                <stop offset="65%" stop-color="#0E1E30" stop-opacity="0.7"/>
                <stop offset="100%" stop-color="#040A12"/>
              </linearGradient>
              <radialGradient id="sk-backlight" cx="50%" cy="80%" r="80%">
                <stop offset="0%" stop-color="rgba(0,120,255,0.18)"/>
                <stop offset="100%" stop-color="transparent"/>
              </radialGradient>
              <linearGradient id="sk-dropper" x1="0%" y1="0%" x2="100%" y2="0%">
                <stop offset="0%" stop-color="#060C14"/>
                <stop offset="40%" stop-color="#101C2C"/>
                <stop offset="60%" stop-color="#1A2C42"/>
                <stop offset="100%" stop-color="#060C14"/>
              </linearGradient>
            </defs>
            <ellipse cx="140" cy="548" rx="60" ry="8" fill="black" opacity="0.5"/>
            <!-- main bottle body (tall dropper) -->
            <rect x="80" y="130" width="120" height="360" rx="8" fill="#030810"/>
            <rect x="80" y="130" width="120" height="360" rx="8" fill="url(#sk-frosted)" opacity="0.85"/>
            <rect x="80" y="130" width="120" height="360" rx="8" fill="url(#sk-backlight)" opacity="0.7"/>
            <!-- frosted glass edge highlight -->
            <rect x="80" y="132" width="2.5" height="356" rx="1.25" fill="#00AEFF" opacity="0.25"/>
            <rect x="197.5" y="132" width="2.5" height="356" rx="1.25" fill="#00AEFF" opacity="0.12"/>
            <!-- inner liquid visible through glass -->
            <rect x="90" y="160" width="100" height="300" rx="6" fill="rgba(0,30,80,0.3)"/>
            <!-- surface refraction lines -->
            <path d="M86 220 Q140 208 194 226" stroke="rgba(0,174,255,0.1)" stroke-width="0.5" fill="none"/>
            <path d="M86 260 Q140 250 194 268" stroke="rgba(0,174,255,0.07)" stroke-width="0.5" fill="none"/>
            <path d="M86 310 Q140 298 194 318" stroke="rgba(0,174,255,0.06)" stroke-width="0.5" fill="none"/>
            <!-- specular streak -->
            <rect x="96" y="150" width="8" height="300" rx="4" fill="white" opacity="0.018"/>
            <!-- label markings -->
            <rect x="90" y="255" width="100" height="0.5" fill="rgba(0,174,255,0.3)"/>
            <rect x="96" y="270" width="88" height="0.4" fill="rgba(0,174,255,0.2)"/>
            <rect x="100" y="285" width="80" height="0.4" fill="rgba(0,174,255,0.15)"/>
            <!-- rim top -->
            <rect x="80" y="126" width="120" height="8" rx="2" fill="#00AEFF" opacity="0.6"/>
            <rect x="80" y="126" width="120" height="4" rx="2" fill="#00AEFF" opacity="0.3"/>
            <!-- neck -->
            <rect x="110" y="72" width="60" height="60" rx="4" fill="url(#sk-dropper)"/>
            <rect x="112" y="74" width="4" height="54" rx="2" fill="white" opacity="0.02"/>
            <rect x="110" y="70" width="60" height="6" rx="2" fill="#00AEFF" opacity="0.5"/>
            <rect x="110" y="128" width="60" height="4" rx="2" fill="rgba(0,174,255,0.35)"/>
            <!-- dropper bulb -->
            <ellipse cx="140" cy="44" rx="28" ry="34" fill="#0A1828"/>
            <ellipse cx="128" cy="36" rx="10" ry="8" fill="rgba(255,255,255,0.025)"/>
            <!-- dropper tip -->
            <rect x="136" y="18" width="8" height="26" rx="4" fill="#030608"/>
            <rect x="136" y="18" width="8" height="10" rx="4" fill="rgba(0,174,255,0.4)"/>
            <!-- bottom rim -->
            <rect x="80" y="484" width="120" height="6" rx="2" fill="rgba(0,80,160,0.4)"/>
            <!-- particles -->
            <circle cx="240" cy="180" r="1.5" fill="#00AEFF" opacity="0.5"/>
            <circle cx="250" cy="300" r="1" fill="#00AEFF" opacity="0.35"/>
            <circle cx="30" cy="260" r="1.5" fill="#00AEFF" opacity="0.3"/>
            <circle cx="25" cy="380" r="1" fill="white" opacity="0.12"/>
          </svg>
        </div>
      </div>
    </div>

  </div><!-- /hero-track -->

  <!-- Hero UI -->
  <div class="hero-ui">
    <div class="hero-ui-top">
      <div class="slide-index">
        <span id="curSlideNum">01</span> / 03
      </div>
      <div class="slide-cat" id="slideCategory">Fragrance · KV 001</div>
    </div>
    <div class="hero-bottom">
      <div class="hero-text">
        <div class="hero-eyebrow">3D Commercial Visualist</div>
        <h1 class="hero-title" id="heroTitle">
          RENDER<br>
          <em>BEYOND</em><br>
          REAL
        </h1>
        <p class="hero-sub" id="heroSub">
          Studio-grade 3D visuals for premium brands.<br>
          Octane · Redshift · 8K output.
        </p>
        <div class="hero-actions">
          <a href="#works" class="btn-view">View Work</a>
          <a href="#case" class="btn-secondary">
            <span>Case Studies</span>
            <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5"><path d="M5 12h14M12 5l7 7-7 7"/></svg>
          </a>
        </div>
      </div>
      <div class="slide-nav" id="slideNav">
        <div class="slide-dot active" data-idx="0"></div>
        <div class="slide-dot" data-idx="1"></div>
        <div class="slide-dot" data-idx="2"></div>
      </div>
    </div>
  </div>
  <div class="hero-progress">
    <div class="hero-progress-bar" id="heroPBar"></div>
  </div>
</section>

<!-- ═══ MARQUEE ═══ -->
<div class="marquee-strip">
  <div class="marquee-inner">
    <span class="mi-item"><span class="mi-dot"></span>Octane Render</span>
    <span class="mi-item"><span class="mi-dot"></span>Redshift</span>
    <span class="mi-item"><span class="mi-dot"></span>Cinema 4D</span>
    <span class="mi-item"><span class="mi-dot"></span>Arnold</span>
    <span class="mi-item"><span class="mi-dot"></span>8K Output</span>
    <span class="mi-item"><span class="mi-dot"></span>Ray-Traced Reflections</span>
    <span class="mi-item"><span class="mi-dot"></span>Studio Lighting</span>
    <span class="mi-item"><span class="mi-dot"></span>Photorealistic CGI</span>
    <span class="mi-item"><span class="mi-dot"></span>Fragrance</span>
    <span class="mi-item"><span class="mi-dot"></span>Technology</span>
    <span class="mi-item"><span class="mi-dot"></span>Skincare</span>
    <span class="mi-item"><span class="mi-dot"></span>Luxury</span>
    <!-- duplicate for loop -->
    <span class="mi-item"><span class="mi-dot"></span>Octane Render</span>
    <span class="mi-item"><span class="mi-dot"></span>Redshift</span>
    <span class="mi-item"><span class="mi-dot"></span>Cinema 4D</span>
    <span class="mi-item"><span class="mi-dot"></span>Arnold</span>
    <span class="mi-item"><span class="mi-dot"></span>8K Output</span>
    <span class="mi-item"><span class="mi-dot"></span>Ray-Traced Reflections</span>
    <span class="mi-item"><span class="mi-dot"></span>Studio Lighting</span>
    <span class="mi-item"><span class="mi-dot"></span>Photorealistic CGI</span>
    <span class="mi-item"><span class="mi-dot"></span>Fragrance</span>
    <span class="mi-item"><span class="mi-dot"></span>Technology</span>
    <span class="mi-item"><span class="mi-dot"></span>Skincare</span>
    <span class="mi-item"><span class="mi-dot"></span>Luxury</span>
  </div>
</div>

<!-- ═══ FLOATING NAV ═══ -->
<nav id="float-nav">
  <div class="fn-item active" data-target="#hero">
    <span class="fn-label">Hero</span>
    <span class="fn-dot"></span>
  </div>
  <div class="fn-item" data-target="#works">
    <span class="fn-label">Work</span>
    <span class="fn-dot"></span>
  </div>
  <div class="fn-item" data-target="#about">
    <span class="fn-label">Studio</span>
    <span class="fn-dot"></span>
  </div>
  <div class="fn-item" data-target="#case">
    <span class="fn-label">Cases</span>
    <span class="fn-dot"></span>
  </div>
  <div class="fn-item" data-target="#caps">
    <span class="fn-label">Services</span>
    <span class="fn-dot"></span>
  </div>
  <div class="fn-item" data-target="#contact">
    <span class="fn-label">Contact</span>
    <span class="fn-dot"></span>
  </div>
</nav>

<!-- ═══ WORKS / BENTO ═══ -->
<section id="works">
  <div class="works-header">
    <div>
      <p class="sec-label reveal">Selected Work</p>
      <h2 class="sec-title reveal d1">Gallery</h2>
    </div>
    <div class="works-count reveal d2">05</div>
  </div>
  <div class="bento">

    <!-- Card 1: large vertical - Fragrance -->
    <div class="bento-card reveal">
      <div class="bc-img">
        <div class="bc-product render-1">
          <svg width="52%" viewBox="0 0 280 500" fill="none">
            <defs>
              <linearGradient id="b1g" x1="0%" y1="0%" x2="100%" y2="0%">
                <stop offset="0%" stop-color="#030911"/>
                <stop offset="22%" stop-color="#0C1E36"/>
                <stop offset="50%" stop-color="#1A3A6E" stop-opacity="0.3"/>
                <stop offset="78%" stop-color="#0C1E36"/>
                <stop offset="100%" stop-color="#030911"/>
              </linearGradient>
            </defs>
            <ellipse cx="140" cy="492" rx="65" ry="7" fill="black" opacity="0.6"/>
            <rect x="72" y="136" width="136" height="304" rx="4" fill="#050D1A"/>
            <rect x="72" y="136" width="136" height="304" rx="4" fill="url(#b1g)"/>
            <rect x="72" y="134" width="136" height="6" rx="2" fill="#00AEFF" opacity="0.7"/>
            <rect x="72" y="432" width="136" height="4" rx="2" fill="rgba(0,80,160,0.4)"/>
            <rect x="72" y="138" width="2" height="296" fill="rgba(0,174,255,0.2)"/>
            <rect x="72" y="248" width="136" height="0.5" fill="rgba(0,174,255,0.22)"/>
            <rect x="80" y="265" width="120" height="0.4" fill="rgba(0,174,255,0.14)"/>
            <rect x="100" y="282" width="80" height="0.4" fill="rgba(0,174,255,0.1)"/>
            <rect x="92" y="152" width="8" height="250" rx="4" fill="white" opacity="0.016"/>
            <!-- neck -->
            <rect x="108" y="90" width="64" height="48" rx="3" fill="#030810"/>
            <rect x="108" y="88" width="64" height="5" rx="2" fill="rgba(0,174,255,0.55)"/>
            <!-- cap -->
            <rect x="90" y="30" width="100" height="62" rx="4" fill="#0A1828"/>
            <rect x="90" y="28" width="100" height="6" rx="2" fill="#00AEFF" opacity="0.5"/>
            <rect x="96" y="34" width="10" height="50" rx="5" fill="white" opacity="0.03"/>
            <ellipse cx="140" cy="31" rx="50" ry="4" fill="rgba(0,174,255,0.15)"/>
            <circle cx="215" cy="160" r="1.5" fill="#00AEFF" opacity="0.6"/>
            <circle cx="225" cy="240" r="1" fill="#00AEFF" opacity="0.4"/>
            <circle cx="50" cy="300" r="1" fill="#00AEFF" opacity="0.35"/>
          </svg>
        </div>
      </div>
      <div class="bc-glass"></div>
      <div class="bc-meta">
        <span class="bc-tag">Fragrance · 8K</span>
        <h3 class="bc-name">Nuit Absolue<br>Eau de Parfum</h3>
        <div class="bc-specs">
          <div class="bc-spec">Software<span>Octane / C4D</span></div>
          <div class="bc-spec">Resolution<span>8192×8192</span></div>
          <div class="bc-spec">Client<span>Maison Eryx</span></div>
        </div>
      </div>
      <div class="bc-arrow">
        <svg viewBox="0 0 24 24"><path d="M7 17L17 7M17 7H7M17 7v10"/></svg>
      </div>
    </div>

    <!-- Card 2: square - Tech -->
    <div class="bento-card reveal d1">
      <div class="bc-img">
        <div class="bc-product render-2">
          <svg width="70%" viewBox="0 0 280 240" fill="none">
            <defs>
              <linearGradient id="b2g" x1="0%" y1="0%" x2="100%" y2="100%">
                <stop offset="0%" stop-color="#0A1422"/>
                <stop offset="100%" stop-color="#040810"/>
              </linearGradient>
            </defs>
            <!-- earbuds case (open) -->
            <!-- case body -->
            <rect x="50" y="80" width="180" height="120" rx="12" fill="url(#b2g)"/>
            <rect x="52" y="82" width="176" height="116" rx="11" fill="none" stroke="rgba(0,174,255,0.12)" stroke-width="0.5"/>
            <!-- case lid (open/tilted) -->
            <path d="M50 80 Q140 20 230 80 L230 82 Q140 28 50 82 Z" fill="#060C18"/>
            <path d="M50 80 Q140 22 230 80" stroke="rgba(0,174,255,0.2)" stroke-width="0.5" fill="none"/>
            <!-- inner housing -->
            <ellipse cx="105" cy="132" rx="36" ry="40" fill="#030609"/>
            <ellipse cx="175" cy="132" rx="36" ry="40" fill="#030609"/>
            <!-- left earbud -->
            <ellipse cx="105" cy="125" rx="18" ry="22" fill="#0A1428"/>
            <ellipse cx="105" cy="125" rx="18" ry="22" fill="none" stroke="rgba(0,174,255,0.15)" stroke-width="0.5"/>
            <ellipse cx="105" cy="125" rx="8" ry="10" fill="#05080E"/>
            <circle cx="98" cy="118" r="3" fill="#00AEFF" opacity="0.7"/>
            <circle cx="98" cy="118" r="6" fill="#00AEFF" opacity="0.1"/>
            <!-- right earbud -->
            <ellipse cx="175" cy="125" rx="18" ry="22" fill="#0A1428"/>
            <ellipse cx="175" cy="125" rx="18" ry="22" fill="none" stroke="rgba(0,174,255,0.1)" stroke-width="0.5"/>
            <ellipse cx="175" cy="125" rx="8" ry="10" fill="#05080E"/>
            <!-- charging indicator dots -->
            <circle cx="120" cy="192" r="2" fill="#00AEFF" opacity="0.8"/>
            <circle cx="128" cy="192" r="2" fill="#00AEFF" opacity="0.5"/>
            <circle cx="136" cy="192" r="2" fill="rgba(0,174,255,0.2)"/>
            <!-- specular on case -->
            <path d="M55 90 Q140 35 225 90" stroke="rgba(255,255,255,0.03)" stroke-width="3" fill="none"/>
            <circle cx="55" cy="90" r="1.5" fill="#00AEFF" opacity="0.5"/>
            <circle cx="230" cy="55" r="1" fill="#00AEFF" opacity="0.4"/>
          </svg>
        </div>
      </div>
      <div class="bc-glass"></div>
      <div class="bc-meta">
        <span class="bc-tag">Technology · 6K</span>
        <h3 class="bc-name">Prism ANC<br>Pro Earbuds</h3>
        <div class="bc-specs">
          <div class="bc-spec">Software<span>Redshift / Maya</span></div>
          <div class="bc-spec">Client<span>Aura Audio</span></div>
        </div>
      </div>
      <div class="bc-arrow">
        <svg viewBox="0 0 24 24"><path d="M7 17L17 7M17 7H7M17 7v10"/></svg>
      </div>
    </div>

    <!-- Card 3: square - Skincare macro -->
    <div class="bento-card reveal d2">
      <div class="bc-img">
        <div class="bc-product render-3">
          <svg width="65%" viewBox="0 0 240 240" fill="none">
            <defs>
              <radialGradient id="b3g" cx="40%" cy="35%" r="70%">
                <stop offset="0%" stop-color="rgba(0,174,255,0.18)"/>
                <stop offset="100%" stop-color="transparent"/>
              </radialGradient>
            </defs>
            <ellipse cx="120" cy="220" rx="70" ry="8" fill="black" opacity="0.5"/>
            <!-- jar body -->
            <rect x="50" y="96" width="140" height="100" rx="8" fill="#040810"/>
            <rect x="50" y="96" width="140" height="100" rx="8" fill="url(#b3g)"/>
            <rect x="52" y="98" width="2" height="94" rx="1" fill="rgba(0,174,255,0.3)"/>
            <!-- rim -->
            <rect x="50" y="90" width="140" height="10" rx="3" fill="#00AEFF" opacity="0.7"/>
            <rect x="50" y="190" width="140" height="6" rx="3" fill="rgba(0,60,140,0.5)"/>
            <!-- lid -->
            <rect x="50" y="30" width="140" height="64" rx="8" fill="#0A1828"/>
            <rect x="50" y="28" width="140" height="8" rx="3" fill="#00AEFF" opacity="0.6"/>
            <rect x="55" y="34" width="14" height="50" rx="7" fill="white" opacity="0.03"/>
            <ellipse cx="120" cy="30" rx="70" ry="5" fill="rgba(0,174,255,0.2)"/>
            <!-- engraving lines on lid -->
            <rect x="65" y="58" width="110" height="0.5" fill="rgba(0,174,255,0.18)"/>
            <rect x="72" y="68" width="96" height="0.4" fill="rgba(0,174,255,0.12)"/>
            <rect x="78" y="78" width="84" height="0.4" fill="rgba(0,174,255,0.08)"/>
            <circle cx="60" cy="32" r="1.5" fill="#00AEFF" opacity="0.6"/>
            <circle cx="185" cy="110" r="1" fill="#00AEFF" opacity="0.4"/>
          </svg>
        </div>
      </div>
      <div class="bc-glass"></div>
      <div class="bc-meta">
        <span class="bc-tag">Skincare · Macro</span>
        <h3 class="bc-name">Cryos Repair<br>Night Cream</h3>
        <div class="bc-specs">
          <div class="bc-spec">Software<span>Arnold / Houdini</span></div>
          <div class="bc-spec">Client<span>Glaciel Lab</span></div>
        </div>
      </div>
      <div class="bc-arrow">
        <svg viewBox="0 0 24 24"><path d="M7 17L17 7M17 7H7M17 7v10"/></svg>
      </div>
    </div>

    <!-- Card 4: wide horizontal - Lifestyle -->
    <div class="bento-card reveal d3">
      <div class="bc-img">
        <div class="bc-product render-4" style="flex-direction:row;gap:5%;">
          <svg width="22%" viewBox="0 0 200 360" fill="none">
            <rect x="60" y="90" width="80" height="230" rx="5" fill="#040810"/>
            <rect x="60" y="88" width="80" height="6" rx="2" fill="rgba(0,174,255,0.6)"/>
            <rect x="60" y="90" width="2" height="226" fill="rgba(0,174,255,0.2)"/>
            <rect x="72" y="54" width="56" height="38" rx="3" fill="#030609"/>
            <rect x="72" y="52" width="56" height="5" rx="2" fill="rgba(0,174,255,0.45)"/>
            <rect x="64" y="16" width="72" height="40" rx="4" fill="#0A1828"/>
            <rect x="64" y="14" width="72" height="5" rx="2" fill="#00AEFF" opacity="0.55"/>
            <rect x="60" y="312" width="80" height="4" rx="2" fill="rgba(0,60,140,0.4)"/>
          </svg>
          <svg width="22%" viewBox="0 0 200 360" fill="none">
            <rect x="55" y="105" width="90" height="210" rx="6" fill="#050A14"/>
            <rect x="55" y="103" width="90" height="6" rx="2" fill="rgba(0,174,255,0.5)"/>
            <rect x="57" y="107" width="2" height="204" fill="rgba(0,174,255,0.15)"/>
            <rect x="70" y="64" width="60" height="43" rx="3" fill="#030710"/>
            <rect x="70" y="62" width="60" height="4" rx="2" fill="rgba(0,174,255,0.4)"/>
            <ellipse cx="100" cy="40" rx="30" ry="36" fill="#080E1C"/>
            <ellipse cx="88" cy="33" rx="10" ry="8" fill="rgba(255,255,255,0.02)"/>
            <rect x="97" y="10" width="6" height="18" rx="3" fill="#020408"/>
            <rect x="55" y="307" width="90" height="4" rx="2" fill="rgba(0,60,140,0.35)"/>
          </svg>
          <svg width="22%" viewBox="0 0 200 360" fill="none">
            <ellipse cx="100" cy="200" rx="68" ry="78" fill="#050A16"/>
            <ellipse cx="100" cy="200" rx="50" ry="58" fill="#040810"/>
            <ellipse cx="100" cy="200" rx="34" ry="40" fill="#030608"/>
            <ellipse cx="100" cy="200" rx="20" ry="24" fill="#020406" stroke="rgba(0,174,255,0.18)" stroke-width="0.5"/>
            <path d="M42 152 Q18 200 42 248" stroke="rgba(0,174,255,0.25)" stroke-width="1.5" fill="none"/>
            <circle cx="72" cy="162" r="3" fill="#00AEFF" opacity="0.85"/>
            <circle cx="72" cy="162" r="7" fill="#00AEFF" opacity="0.12"/>
            <ellipse cx="78" cy="180" rx="12" ry="9" fill="rgba(255,255,255,0.02)"/>
          </svg>
        </div>
      </div>
      <div class="bc-glass"></div>
      <div class="bc-meta">
        <span class="bc-tag">Multi-Product · Campaign</span>
        <h3 class="bc-name">Aura Collection<br>Spring / Summer 2025</h3>
        <div class="bc-specs">
          <div class="bc-spec">Deliverables<span>18 Assets</span></div>
          <div class="bc-spec">Format<span>8K + Social</span></div>
          <div class="bc-spec">Timeline<span>3 Weeks</span></div>
        </div>
      </div>
      <div class="bc-arrow">
        <svg viewBox="0 0 24 24"><path d="M7 17L17 7M17 7H7M17 7v10"/></svg>
      </div>
    </div>

    <!-- Card 5: square - Architectural macro -->
    <div class="bento-card reveal d4">
      <div class="bc-img">
        <div class="bc-product render-5">
          <svg width="68%" viewBox="0 0 260 260" fill="none">
            <defs>
              <radialGradient id="b5g" cx="35%" cy="30%" r="70%">
                <stop offset="0%" stop-color="rgba(0,120,240,0.2)"/>
                <stop offset="100%" stop-color="transparent"/>
              </radialGradient>
            </defs>
            <!-- watch extreme close-up / macro -->
            <circle cx="130" cy="130" rx="100" r="100" fill="#040810"/>
            <circle cx="130" cy="130" rx="100" r="100" fill="url(#b5g)"/>
            <circle cx="130" cy="130" r="100" fill="none" stroke="#00AEFF" stroke-width="0.5" opacity="0.5"/>
            <circle cx="130" cy="130" r="90" fill="none" stroke="rgba(0,174,255,0.08)" stroke-width="0.5"/>
            <!-- dial -->
            <circle cx="130" cy="130" r="82" fill="#02050A"/>
            <!-- applied indices -->
            <rect x="127" y="52" width="6" height="12" rx="1" fill="#00AEFF" opacity="0.8"/>
            <rect x="210" y="127" width="12" height="6" rx="1" fill="#00AEFF" opacity="0.5"/>
            <rect x="127" y="208" width="6" height="12" rx="1" fill="#00AEFF" opacity="0.5"/>
            <rect x="48" y="127" width="12" height="6" rx="1" fill="#00AEFF" opacity="0.5"/>
            <!-- minute markers -->
            <rect x="128.5" y="56" width="3" height="6" rx="0.5" fill="rgba(0,174,255,0.3)" transform="rotate(30,130,130)"/>
            <rect x="128.5" y="56" width="3" height="6" rx="0.5" fill="rgba(0,174,255,0.3)" transform="rotate(60,130,130)"/>
            <rect x="128.5" y="56" width="3" height="6" rx="0.5" fill="rgba(0,174,255,0.3)" transform="rotate(120,130,130)"/>
            <rect x="128.5" y="56" width="3" height="6" rx="0.5" fill="rgba(0,174,255,0.3)" transform="rotate(150,130,130)"/>
            <rect x="128.5" y="56" width="3" height="6" rx="0.5" fill="rgba(0,174,255,0.3)" transform="rotate(210,130,130)"/>
            <rect x="128.5" y="56" width="3" height="6" rx="0.5" fill="rgba(0,174,255,0.3)" transform="rotate(240,130,130)"/>
            <rect x="128.5" y="56" width="3" height="6" rx="0.5" fill="rgba(0,174,255,0.3)" transform="rotate(300,130,130)"/>
            <rect x="128.5" y="56" width="3" height="6" rx="0.5" fill="rgba(0,174,255,0.3)" transform="rotate(330,130,130)"/>
            <!-- hands -->
            <line x1="130" y1="130" x2="130" y2="72" stroke="#00AEFF" stroke-width="2" stroke-linecap="round"/>
            <line x1="130" y1="130" x2="170" y2="148" stroke="white" stroke-width="1.5" stroke-linecap="round" opacity="0.7"/>
            <circle cx="130" cy="130" r="4" fill="#00AEFF"/>
            <circle cx="130" cy="130" r="2.5" fill="#02050A"/>
            <!-- crown -->
            <rect x="232" y="122" width="14" height="16" rx="4" fill="#0A1828" stroke="rgba(0,174,255,0.25)" stroke-width="0.5"/>
            <circle cx="215" cy="72" r="1.5" fill="#00AEFF" opacity="0.6"/>
          </svg>
        </div>
      </div>
      <div class="bc-glass"></div>
      <div class="bc-meta">
        <span class="bc-tag">Timepiece · Macro</span>
        <h3 class="bc-name">Meridian S<br>Chronograph</h3>
        <div class="bc-specs">
          <div class="bc-spec">Render<span>Keyshot Pro</span></div>
          <div class="bc-spec">Client<span>Valence Watch</span></div>
        </div>
      </div>
      <div class="bc-arrow">
        <svg viewBox="0 0 24 24"><path d="M7 17L17 7M17 7H7M17 7v10"/></svg>
      </div>
    </div>

  </div><!-- /bento -->
</section>

<!-- ═══ ABOUT ═══ -->
<section id="about">
  <div class="about-left">
    <div class="about-line"><span class="about-line-text">Studio Profile</span></div>
    <p class="sec-label reveal">About</p>
    <h2 class="sec-title reveal d1">Visual<br>Architecture<br><em style="color:var(--e-blue);font-style:normal;">Perfected</em></h2>
    <p class="about-desc reveal d2">
      AXIOM operates at the intersection of technical precision and aesthetic ambition. We architect high-fidelity 3D visuals that communicate material quality, emotional resonance, and brand positioning—simultaneously.
    </p>
    <p class="about-desc reveal d2">
      Every render begins with deep material study. Glass refraction indices, metallic BRDF curves, subsurface scattering coefficients—these aren't side notes, they're the foundation of photorealism.
    </p>
    <div class="about-stats">
      <div class="reveal d2">
        <div class="a-stat-n">8K<em>+</em></div>
        <div class="a-stat-l">Max output resolution</div>
      </div>
      <div class="reveal d3">
        <div class="a-stat-n">180<em>+</em></div>
        <div class="a-stat-l">Projects delivered</div>
      </div>
      <div class="reveal d2">
        <div class="a-stat-n">12<em>+</em></div>
        <div class="a-stat-l">Years rendering</div>
      </div>
      <div class="reveal d3">
        <div class="a-stat-n">24<em>h</em></div>
        <div class="a-stat-l">Rapid turnaround option</div>
      </div>
    </div>
  </div>
  <div class="about-right reveal d1">
    <div class="about-render-wrap">
      <div style="width:100%;height:100%;display:flex;align-items:center;justify-content:center;position:relative;z-index:1;min-height:520px;">
        <svg width="55%" viewBox="0 0 300 480" fill="none">
          <defs>
            <linearGradient id="ab1" x1="0%" y1="0%" x2="100%" y2="0%">
              <stop offset="0%" stop-color="#030912"/>
              <stop offset="22%" stop-color="#0C1E38"/>
              <stop offset="50%" stop-color="#1A3870" stop-opacity="0.35"/>
              <stop offset="78%" stop-color="#0C1E38"/>
              <stop offset="100%" stop-color="#030912"/>
            </linearGradient>
            <radialGradient id="ab2" cx="55%" cy="35%" r="60%">
              <stop offset="0%" stop-color="rgba(0,100,220,0.25)"/>
              <stop offset="100%" stop-color="transparent"/>
            </radialGradient>
          </defs>
          <ellipse cx="150" cy="472" rx="70" ry="8" fill="black" opacity="0.5"/>
          <rect x="78" y="140" width="144" height="290" rx="5" fill="#04080E"/>
          <rect x="78" y="140" width="144" height="290" rx="5" fill="url(#ab1)"/>
          <rect x="78" y="140" width="144" height="290" rx="5" fill="url(#ab2)"/>
          <rect x="78" y="138" width="144" height="6" rx="2" fill="#00AEFF" opacity="0.75"/>
          <rect x="78" y="424" width="144" height="5" rx="2" fill="rgba(0,70,160,0.45)"/>
          <rect x="78" y="142" width="2.5" height="280" fill="rgba(0,174,255,0.22)"/>
          <rect x="90" y="258" width="120" height="0.5" fill="rgba(0,174,255,0.22)"/>
          <rect x="96" y="275" width="108" height="0.4" fill="rgba(0,174,255,0.14)"/>
          <rect x="94" y="156" width="8" height="240" rx="4" fill="white" opacity="0.015"/>
          <rect x="110" y="96" width="80" height="46" rx="3" fill="#030710"/>
          <rect x="110" y="94" width="80" height="5" rx="2" fill="rgba(0,174,255,0.5)"/>
          <rect x="94" y="32" width="112" height="66" rx="5" fill="#08142A"/>
          <rect x="94" y="30" width="112" height="6" rx="2" fill="#00AEFF" opacity="0.55"/>
          <rect x="100" y="36" width="12" height="54" rx="6" fill="white" opacity="0.025"/>
          <ellipse cx="150" cy="32" rx="56" ry="5" fill="rgba(0,174,255,0.18)"/>
          <circle cx="228" cy="170" r="1.5" fill="#00AEFF" opacity="0.6"/>
          <circle cx="240" cy="270" r="1" fill="#00AEFF" opacity="0.4"/>
          <circle cx="55" cy="320" r="1.5" fill="#00AEFF" opacity="0.3"/>
        </svg>
      </div>
    </div>
    <div class="about-accent">
      <div class="about-accent-text">
        Octane<br>Certified<br>Artist
      </div>
    </div>
  </div>
</section>

<!-- ═══ CASE STUDY ═══ -->
<section id="case">
  <div class="case-header">
    <div>
      <p class="sec-label reveal">Deep Dive</p>
      <h2 class="sec-title reveal d1">Case Study<br><em style="color:var(--e-blue);font-style:normal;">001</em></h2>
    </div>
    <p class="case-h-right reveal d1">
      An in-depth look at the production pipeline behind the Nuit Absolue campaign—from brief interpretation to final 8K delivery. Every lighting decision, render pass, and post-processing choice, documented.
    </p>
  </div>
  <div class="case-split">
    <!-- sticky text column -->
    <div class="case-text-col">
      <div class="case-chapter reveal">
        <div class="chapter-num">Chapter 01</div>
        <h3 class="chapter-title">The Brief:<br>Translating Scent</h3>
        <p class="chapter-body">
          Maison Eryx needed a visual that could communicate the olfactory complexity of Nuit Absolue—a fragrance built on aged oud, black amber, and midnight rose—without a single spoken word. The challenge: convert an invisible sensory experience into a tangible visual language.
        </p>
        <p class="chapter-body" style="margin-top:1rem;">
          Our creative direction centered on controlled obscurity. The bottle would be photographed in a state of near-emergence from darkness, lit by a single directional source that referenced the precision of a jeweler's loupe.
        </p>
        <div class="case-spec-table">
          <div class="cst-row"><span class="cst-k">Client</span><span class="cst-v">Maison Eryx</span></div>
          <div class="cst-row"><span class="cst-k">Category</span><span class="cst-v">Luxury Fragrance</span></div>
          <div class="cst-row"><span class="cst-k">Deliverables</span><span class="cst-v">12 × 8K Stills + 3 × 15s Clips</span></div>
          <div class="cst-row"><span class="cst-k">Software</span><span class="cst-v">Cinema 4D, Octane Render 2024</span></div>
          <div class="cst-row"><span class="cst-k">Timeline</span><span class="cst-v">4 Weeks</span></div>
        </div>
      </div>
      <div class="case-chapter reveal">
        <div class="chapter-num">Chapter 02</div>
        <h3 class="chapter-title">Lighting Strategy:<br>The One-Light Doctrine</h3>
        <p class="chapter-body">
          We ran 47 lighting tests over two days. Every multi-light setup felt decorative—it diffused the mystery. The breakthrough came when we removed everything but a single 12K IES area light at 28° above the bottle's long axis.
        </p>
        <p class="chapter-body" style="margin-top:1rem;">
          The caustic refraction through the glass body produced natural, unpredictable light patterns on the background plane—eliminating the need for any supplemental fill. Shadow became a compositional element, not an absence of light.
        </p>
        <div class="case-spec-table">
          <div class="cst-row"><span class="cst-k">Key Light</span><span class="cst-v">12K IES · 5800K</span></div>
          <div class="cst-row"><span class="cst-k">Angle</span><span class="cst-v">28° above axis</span></div>
          <div class="cst-row"><span class="cst-k">Fill</span><span class="cst-v">None (caustic only)</span></div>
          <div class="cst-row"><span class="cst-k">Background</span><span class="cst-v">00% reflectance, 4% spec</span></div>
        </div>
      </div>
      <div class="case-chapter reveal">
        <div class="chapter-num">Chapter 03</div>
        <h3 class="chapter-title">Material Architecture:<br>Glass BRDF Calibration</h3>
        <p class="chapter-body">
          The bottle's glass required seven material passes to achieve the correct IOR gradient—standard glass (1.52) at the surface, tapering toward the liquid interface where the oud-colored fluid sits. Subsurface scattering was added at 0.3% intensity to simulate the microscopic surface texture of luxury-grade crystal.
        </p>
      </div>
    </div>
    <!-- scrolling image column -->
    <div class="case-img-col">
      <div class="ci-block">
        <div class="ci-inner cir-1">
          <svg width="35%" viewBox="0 0 260 500" fill="none">
            <defs>
              <linearGradient id="cg1" x1="0%" y1="0%" x2="100%" y2="0%">
                <stop offset="0%" stop-color="#030910" stop-opacity="0.98"/>
                <stop offset="22%" stop-color="#0C1C30" stop-opacity="0.9"/>
                <stop offset="50%" stop-color="#163260" stop-opacity="0.3"/>
                <stop offset="78%" stop-color="#0C1C30" stop-opacity="0.9"/>
                <stop offset="100%" stop-color="#030910" stop-opacity="0.98"/>
              </linearGradient>
            </defs>
            <ellipse cx="130" cy="490" rx="58" ry="8" fill="black" opacity="0.5"/>
            <rect x="70" y="136" width="120" height="310" rx="4" fill="#050D1A"/>
            <rect x="70" y="136" width="120" height="310" rx="4" fill="url(#cg1)"/>
            <rect x="70" y="134" width="120" height="5" rx="2" fill="#00AEFF" opacity="0.75"/>
            <rect x="70" y="438" width="120" height="4" rx="2" fill="rgba(0,80,160,0.4)"/>
            <rect x="70" y="138" width="2" height="296" fill="rgba(0,174,255,0.22)"/>
            <path d="M76 230 Q130 218 184 238" stroke="rgba(0,174,255,0.14)" stroke-width="0.5" fill="none"/>
            <path d="M76 268 Q130 256 184 276" stroke="rgba(0,174,255,0.09)" stroke-width="0.5" fill="none"/>
            <rect x="80" y="250" width="100" height="0.5" fill="rgba(0,174,255,0.22)"/>
            <rect x="86" y="266" width="88" height="0.4" fill="rgba(0,174,255,0.14)"/>
            <rect x="86" y="154" width="6" height="250" rx="3" fill="white" opacity="0.015"/>
            <rect x="100" y="88" width="60" height="50" rx="3" fill="#030810"/>
            <rect x="100" y="86" width="60" height="5" rx="2" fill="rgba(0,174,255,0.5)"/>
            <rect x="84" y="24" width="92" height="66" rx="5" fill="#08142A"/>
            <rect x="84" y="22" width="92" height="6" rx="2" fill="#00AEFF" opacity="0.6"/>
            <rect x="90" y="28" width="10" height="56" rx="5" fill="white" opacity="0.025"/>
            <ellipse cx="130" cy="24" rx="46" ry="5" fill="rgba(0,174,255,0.2)"/>
            <circle cx="200" cy="160" r="1.5" fill="#00AEFF" opacity="0.6"/>
            <circle cx="210" cy="250" r="1" fill="#00AEFF" opacity="0.4"/>
            <circle cx="45" cy="310" r="1" fill="#00AEFF" opacity="0.3"/>
          </svg>
        </div>
        <div class="ci-caption">
          <span class="ci-cap-label">Hero Shot · Single Light · Final Grade</span>
          <span class="ci-cap-num">01</span>
        </div>
      </div>
      <div class="ci-block">
        <div class="ci-inner cir-2" style="min-height:380px;">
          <!-- wireframe / lighting breakdown -->
          <svg width="75%" viewBox="0 0 480 280" fill="none">
            <!-- grid floor -->
            <line x1="0" y1="250" x2="480" y2="250" stroke="rgba(0,174,255,0.15)" stroke-width="0.5"/>
            <line x1="80" y1="100" x2="80" y2="250" stroke="rgba(0,174,255,0.1)" stroke-width="0.5"/>
            <line x1="160" y1="80" x2="160" y2="250" stroke="rgba(0,174,255,0.1)" stroke-width="0.5"/>
            <line x1="240" y1="70" x2="240" y2="250" stroke="rgba(0,174,255,0.12)" stroke-width="0.5"/>
            <line x1="320" y1="80" x2="320" y2="250" stroke="rgba(0,174,255,0.1)" stroke-width="0.5"/>
            <line x1="400" y1="100" x2="400" y2="250" stroke="rgba(0,174,255,0.08)" stroke-width="0.5"/>
            <!-- perspective grid -->
            <line x1="0" y1="250" x2="240" y2="70" stroke="rgba(0,174,255,0.05)" stroke-width="0.3"/>
            <line x1="480" y1="250" x2="240" y2="70" stroke="rgba(0,174,255,0.05)" stroke-width="0.3"/>
            <line x1="0" y1="220" x2="480" y2="220" stroke="rgba(0,174,255,0.06)" stroke-width="0.3"/>
            <line x1="0" y1="190" x2="480" y2="190" stroke="rgba(0,174,255,0.05)" stroke-width="0.3"/>
            <!-- wireframe bottle -->
            <rect x="204" y="100" width="72" height="130" rx="3" fill="none" stroke="rgba(0,174,255,0.45)" stroke-width="0.8"/>
            <rect x="218" y="72" width="44" height="30" rx="2" fill="none" stroke="rgba(0,174,255,0.3)" stroke-width="0.6"/>
            <rect x="210" y="38" width="60" height="36" rx="3" fill="none" stroke="rgba(0,174,255,0.35)" stroke-width="0.7"/>
            <!-- cross sections -->
            <line x1="204" y1="160" x2="276" y2="160" stroke="rgba(0,174,255,0.2)" stroke-width="0.4"/>
            <line x1="240" y1="100" x2="240" y2="230" stroke="rgba(0,174,255,0.15)" stroke-width="0.4"/>
            <!-- light source indicator -->
            <circle cx="360" cy="30" r="8" fill="none" stroke="#00AEFF" stroke-width="1" opacity="0.7"/>
            <circle cx="360" cy="30" r="3" fill="#00AEFF" opacity="0.6"/>
            <!-- light rays -->
            <line x1="355" y1="38" x2="250" y2="120" stroke="rgba(0,174,255,0.3)" stroke-width="0.5" stroke-dasharray="4,4"/>
            <line x1="360" y1="38" x2="250" y2="150" stroke="rgba(0,174,255,0.2)" stroke-width="0.5" stroke-dasharray="4,4"/>
            <line x1="365" y1="38" x2="255" y2="170" stroke="rgba(0,174,255,0.15)" stroke-width="0.5" stroke-dasharray="4,4"/>
            <!-- annotation -->
            <text x="370" y="26" font-size="8" fill="rgba(0,174,255,0.7)" font-family="'Barlow', sans-serif" letter-spacing="1">KEY LIGHT · 12K IES</text>
            <text x="370" y="36" font-size="7" fill="rgba(0,174,255,0.4)" font-family="'Barlow', sans-serif" letter-spacing="0.5">5800K · 28° ABOVE AXIS</text>
            <!-- camera -->
            <rect x="20" y="180" width="30" height="20" rx="2" fill="none" stroke="rgba(0,174,255,0.3)" stroke-width="0.7"/>
            <circle cx="25" cy="176" r="4" fill="none" stroke="rgba(0,174,255,0.25)" stroke-width="0.5"/>
            <line x1="35" y1="180" x2="205" y2="155" stroke="rgba(0,174,255,0.15)" stroke-width="0.4" stroke-dasharray="3,3"/>
            <text x="10" y="212" font-size="7" fill="rgba(0,174,255,0.4)" font-family="'Barlow', sans-serif" letter-spacing="0.5">CAMERA · 85mm f/2.8</text>
          </svg>
        </div>
        <div class="ci-caption">
          <span class="ci-cap-label">Lighting Diagram · Stage Setup</span>
          <span class="ci-cap-num">02</span>
        </div>
      </div>
      <div class="ci-block">
        <div class="ci-inner cir-3">
          <svg width="35%" viewBox="0 0 260 520" fill="none">
            <!-- extreme close up / macro of cap detail -->
            <defs>
              <linearGradient id="cg3" x1="0%" y1="0%" x2="100%" y2="100%">
                <stop offset="0%" stop-color="#0A1830"/>
                <stop offset="50%" stop-color="#1A3060"/>
                <stop offset="100%" stop-color="#060C18"/>
              </linearGradient>
              <radialGradient id="cg3r" cx="30%" cy="25%" r="70%">
                <stop offset="0%" stop-color="rgba(0,174,255,0.25)"/>
                <stop offset="100%" stop-color="transparent"/>
              </radialGradient>
            </defs>
            <!-- macro cap section enlarged -->
            <rect x="30" y="60" width="200" height="200" rx="8" fill="url(#cg3)"/>
            <rect x="30" y="60" width="200" height="200" rx="8" fill="url(#cg3r)"/>
            <!-- machined grooves -->
            <rect x="30" y="100" width="200" height="1" fill="rgba(0,174,255,0.35)"/>
            <rect x="30" y="120" width="200" height="0.5" fill="rgba(0,174,255,0.2)"/>
            <rect x="30" y="140" width="200" height="0.5" fill="rgba(0,174,255,0.15)"/>
            <rect x="30" y="160" width="200" height="0.5" fill="rgba(0,174,255,0.12)"/>
            <rect x="30" y="180" width="200" height="0.5" fill="rgba(0,174,255,0.1)"/>
            <rect x="30" y="200" width="200" height="0.5" fill="rgba(0,174,255,0.08)"/>
            <rect x="30" y="218" width="200" height="1" fill="rgba(0,174,255,0.28)"/>
            <!-- specular edge left -->
            <rect x="30" y="62" width="3" height="196" fill="#00AEFF" opacity="0.4"/>
            <!-- broad specular streak -->
            <rect x="42" y="65" width="18" height="188" fill="white" opacity="0.04"/>
            <!-- logo/brand mark impression -->
            <rect x="90" y="130" width="80" height="0.8" fill="rgba(0,174,255,0.4)"/>
            <rect x="100" y="148" width="60" height="0.6" fill="rgba(0,174,255,0.28)"/>
            <rect x="108" y="166" width="44" height="0.6" fill="rgba(0,174,255,0.2)"/>
            <!-- bottom section (neck junction) -->
            <rect x="30" y="260" width="200" height="80" rx="4" fill="#040810"/>
            <rect x="30" y="258" width="200" height="5" rx="2" fill="#00AEFF" opacity="0.6"/>
            <!-- glass bottle top (below cap) -->
            <rect x="30" y="340" width="200" height="120" rx="3" fill="#050D1A"/>
            <rect x="32" y="342" width="2.5" height="114" fill="rgba(0,174,255,0.2)"/>
            <ellipse cx="130" cy="350" rx="55" ry="6" fill="rgba(0,80,200,0.12)"/>
            <circle cx="215" cy="75" r="1.5" fill="#00AEFF" opacity="0.65"/>
            <circle cx="220" cy="200" r="1" fill="#00AEFF" opacity="0.4"/>
            <circle cx="40" cy="360" r="1" fill="#00AEFF" opacity="0.3"/>
          </svg>
        </div>
        <div class="ci-caption">
          <span class="ci-cap-label">Macro Detail · Cap Machining</span>
          <span class="ci-cap-num">03</span>
        </div>
      </div>
    </div>
  </div>
</section>

<!-- ═══ CAPABILITIES ═══ -->
<section id="caps">
  <div style="display:flex;align-items:flex-end;justify-content:space-between;margin-bottom:1rem;">
    <div>
      <p class="sec-label reveal">Capabilities</p>
      <h2 class="sec-title reveal d1">Services</h2>
    </div>
  </div>
  <div class="caps-grid">
    <div class="cap-card reveal">
      <div class="cap-num">01</div>
      <h3 class="cap-title">3D Product Visualization</h3>
      <p class="cap-desc">Studio-grade stills and turntables. Photorealistic renders at up to 8K resolution. Every material meticulously calibrated.</p>
    </div>
    <div class="cap-card reveal d1">
      <div class="cap-num">02</div>
      <h3 class="cap-title">Campaign KV Production</h3>
      <p class="cap-desc">Full creative direction and 3D production for hero key visuals. From brief to delivery, single-source accountability.</p>
    </div>
    <div class="cap-card reveal d2">
      <div class="cap-num">03</div>
      <h3 class="cap-title">Motion & Animation</h3>
      <p class="cap-desc">Product reveals, hero loops, and social content. Optimized for 24fps cinema-grade or 60fps digital delivery.</p>
    </div>
    <div class="cap-card reveal d3">
      <div class="cap-num">04</div>
      <h3 class="cap-title">Material Development</h3>
      <p class="cap-desc">Custom BRDF and shader development. Glass, metal, fabric, ceramic—every material built from spectral data, not assumptions.</p>
    </div>
  </div>
</section>

<!-- ═══ CONTACT ═══ -->
<section id="contact">
  <div class="contact-bg"></div>
  <div class="contact-grid"></div>
  <div class="contact-inner">
    <p class="contact-kicker reveal">Available for commissions</p>
    <h2 class="contact-title reveal d1">START<br>YOUR<br><em style="color:var(--e-blue);font-style:normal;">PROJECT</em></h2>
    <p class="contact-sub reveal d2">
      Every brief deserves a render that stops the scroll.<br>Let's build something extraordinary.
    </p>
    <a href="mailto:studio@axiom.cgi" class="contact-email reveal d2">studio@axiom.cgi</a>
    <div class="contact-links reveal d3">
      <a href="#" class="c-link">Instagram</a>
      <a href="#" class="c-link">Behance</a>
      <a href="#" class="c-link">LinkedIn</a>
      <a href="#" class="c-link">ArtStation</a>
    </div>
  </div>
</section>

<footer>
  <div class="f-logo">AXIOM<sup style="font-size:8px;color:var(--e-blue);"> ®</sup></div>
  <div class="f-copy">© 2025 AXIOM Studio. All rights reserved.</div>
  <div class="f-soc">
    <a href="#">Instagram</a>
    <a href="#">Behance</a>
    <a href="#">ArtStation</a>
  </div>
</footer>

<!-- ═══════════════════════════════ SCRIPTS ═══════════════════════ -->
<script>
/* ── CURSOR ── */
const curDot = document.getElementById('cur-dot');
const curRing = document.getElementById('cur-ring');
let mx=0,my=0,rx=0,ry=0;
document.addEventListener('mousemove', e=>{
  mx=e.clientX; my=e.clientY;
  curDot.style.left=mx+'px'; curDot.style.top=my+'px';
});
(function ringLoop(){
  rx+=(mx-rx)*0.1; ry+=(my-ry)*0.1;
  curRing.style.left=rx+'px'; curRing.style.top=ry+'px';
  requestAnimationFrame(ringLoop);
})();
document.querySelectorAll('a,button,.bento-card,.cap-card,.fn-item,.slide-dot').forEach(el=>{
  el.addEventListener('mouseenter',()=>document.body.classList.add('link-hover'));
  el.addEventListener('mouseleave',()=>document.body.classList.remove('link-hover'));
});

/* ── PRELOADER ── */
const preEl=document.getElementById('preloader');
const preNum=document.getElementById('pre-num');
const preBar=document.getElementById('pre-bar');
let p=0, preInt=setInterval(()=>{
  p+=Math.random()*3+0.5;
  if(p>=100){ p=100; clearInterval(preInt);
    setTimeout(()=>{ preEl.classList.add('done'); startHero(); },300); }
  preNum.textContent=String(Math.floor(p)).padStart(3,'0');
  preBar.style.width=p+'%';
},40);

/* ── HERO CAROUSEL ── */
const slides=document.querySelectorAll('.h-slide');
const dots=document.querySelectorAll('.slide-dot');
const pBar=document.getElementById('heroPBar');
const catEl=document.getElementById('slideCategory');
const slideNumEl=document.getElementById('curSlideNum');
const titles=['RENDER\n<em>BEYOND</em>\nREAL','AUDIO\n<em>MEETS</em>\nDESIGN','SKIN\n<em>SCIENCE</em>\nVISUALISED'];
const subs=[
  'Studio-grade 3D visuals for premium brands.<br>Octane · Redshift · 8K output.',
  'Product renders that communicate material precision.<br>Technology visualised with clarity.',
  'Translating complex formulations into visual luxury.<br>Science rendered beautiful.'
];
let cur=0, autoTimer=null;

function goSlide(n){
  slides[cur].classList.remove('active');
  slides[cur].classList.add('exiting');
  setTimeout(()=>slides[cur].classList.remove('exiting'),1400);
  dots[cur].classList.remove('active');
  cur=n;
  slides[cur].classList.add('active');
  dots[cur].classList.add('active');
  const parts=slides[cur].dataset.cat;
  catEl.textContent=parts;
  slideNumEl.textContent=String(cur+1).padStart(2,'0');
  document.getElementById('heroTitle').innerHTML=titles[cur].replace(/\n/g,'<br>');
  document.getElementById('heroSub').innerHTML=subs[cur];
  pBar.classList.remove('running');
  pBar.classList.add('reset');
  void pBar.offsetWidth;
  pBar.classList.remove('reset');
  requestAnimationFrame(()=>{ pBar.classList.add('running'); });
}
function nextSlide(){ goSlide((cur+1)%slides.length); }
function startHero(){
  pBar.classList.add('running');
  autoTimer=setInterval(nextSlide,5000);
}
dots.forEach(d=>{ d.addEventListener('click',()=>{ clearInterval(autoTimer); goSlide(parseInt(d.dataset.idx)); autoTimer=setInterval(nextSlide,5000); }); });

/* ── NAV SCROLL ── */
const navEl=document.getElementById('nav');
window.addEventListener('scroll',()=>{ navEl.classList.toggle('scrolled',window.scrollY>60); updateFloatNav(); });

/* ── FLOATING NAV ── */
const fnItems=document.querySelectorAll('.fn-item');
const sections=['#hero','#works','#about','#case','#caps','#contact'];
fnItems.forEach(item=>{
  item.addEventListener('click',()=>{
    const t=document.querySelector(item.dataset.target);
    if(t) t.scrollIntoView({behavior:'smooth'});
  });
});
function updateFloatNav(){
  const sy=window.scrollY+window.innerHeight/2;
  sections.forEach((sel,i)=>{
    const el=document.querySelector(sel);
    if(!el) return;
    const top=el.offsetTop, bot=top+el.offsetHeight;
    fnItems[i].classList.toggle('active',sy>=top&&sy<bot);
  });
}

/* ── REVEALS ── */
const revObs=new IntersectionObserver(entries=>{
  entries.forEach(e=>{ if(e.isIntersecting){ e.target.classList.add('vis'); revObs.unobserve(e.target); } });
},{threshold:0.1,rootMargin:'0px 0px -60px 0px'});
document.querySelectorAll('.reveal').forEach(el=>revObs.observe(el));

/* ── BLUR LOAD ── */
const blurObs=new IntersectionObserver(entries=>{
  entries.forEach(e=>{ if(e.isIntersecting){ setTimeout(()=>e.target.classList.add('loaded'),100); blurObs.unobserve(e.target); } });
},{threshold:0.12});
document.querySelectorAll('.bento-card,.ci-block').forEach(el=>blurObs.observe(el));

/* ── PARALLAX BENTO ── */
document.querySelectorAll('.bento-card').forEach(card=>{
  card.addEventListener('mousemove',e=>{
    const r=card.getBoundingClientRect();
    const x=(e.clientX-r.left)/r.width-0.5;
    const y=(e.clientY-r.top)/r.height-0.5;
    const img=card.querySelector('.bc-img');
    if(img) img.style.transform=`scale(1.04) translate(${x*14}px,${y*10}px)`;
  });
  card.addEventListener('mouseleave',()=>{
    const img=card.querySelector('.bc-img');
    if(img) img.style.transform='';
  });
});

/* ── HERO PARALLAX ── */
const heroBgs=document.querySelectorAll('.h-slide-bg');
window.addEventListener('scroll',()=>{
  const s=window.scrollY;
  if(s<window.innerHeight*1.5){
    heroBgs.forEach(bg=>{ bg.style.transform=`translateY(${s*0.25}px)`; });
  }
}, {passive:true});
</script>
</body>
</html>
