Build a "caffeine.html" page for my dashboard — a predictive ENERGY-CURVE caffeine
tracker — using our template.html design system. Match our look exactly: same :root
tokens, fonts, dark background with the drifting glow + dot texture, glass .gm-card
sections, section-title headers. Include lock.js, the supabase script, sync.js, and
topbar.js like the other pages. Page title "Caffeine". When done, add a bento tile
(·06, emoji ☕, accent #C9A36B, subtitle "Intake & timing", links to caffeine.html)
to index.html, then commit and push to GitHub.

DATA SOURCES (read, degrade gracefully if missing):
- Body weight from po_water_v1.profile.weightKg (+ weightUnit) → personalized limit.
- WHOOP via whoop_tokens_v1 → fetch /recovery and /activity/sleep through
  /api/whoop-data (refresh through /api/whoop-refresh on 401). Use recovery_score,
  sleep onset (start) → bedtime hour, and sleep end → wake hour.

PHARMACOKINETICS: caffeine half-life 5 h, peak at +45 min, FDA ceiling 400 mg/day.
active(dose,t) = dose * 0.5^(hoursSince/5). Per-drink milestones: peak (+45min),
half-gone (+5h), cleared (when its contribution drops under 10 mg).

ENERGY MODEL (0–100, sampled every 10 min across 24h) = circadian − lunch dip −
sleep pressure + morning cortisol bump + caffeine boost:
- circadian(t) = 50 + 18*cos(2π(t−16.5)/24)   (peak ~16:30, trough ~04:30)
- lunchDip(t)  = 10*gaussian(t, mu=14, sd=1.4)
- sleepPressure = clamp(hoursAwake,0,24) * 1.7
- morningBump  = 10*gaussian(t−wake, mu=1.2, sd=0.9)
- caffeineBoost = 34*(1 − e^(−activeMg/110))   (saturating)
- asleep (t<wake or t>=bedtime) → clamp(circadian−34, 3, 20)
Defaults wake 7:00, bedtime 23:00 until WHOOP overrides.

SECTIONS:
1) "Your Day": a score RING (0–100, color green≥62 / amber≥40 / red) showing energy
   now + a state word (Peak/High/Steady/Dip/Low) + "RIGHT NOW" + "Hover/drag the curve".
   A glowing SVG energy curve (12A→12A axis), night regions shaded, a "now" line+dot.
   A LINE / BARS / STACK toggle (STACK shows caffeine contribution stacked over the
   no-caffeine baseline). Drag/hover to scrub — the ring + label update to that hour.
   Footer band: Peak focus window, Predicted crash, Last coffee by.
2) "Smart Timing": three tiles — Most productive (peak time + window + score),
   You'll crash (steepest sustained drop in next 8h, only if significant, + how low),
   Coffee cutoff (bedtime − 5*log2(95/30) h, to stay <30 mg by bedtime) — plus a
   personalized tips list (front-load timing, cutoff vs bedtime, hydration, beat-the-
   crash with daylight, low-WHOOP-recovery warning).
3) "Today": daily mg bar vs limit (green→amber→red), "X mg active now", personalized
   note (target ≈ 3 mg/kg from weight; ceiling drops to 200/300 mg on low recovery).
4) "Log a Drink": a SEARCH bar over ~60 researched drinks (coffee, espresso, tea,
   energy drinks, soda, pre-workout, chocolate — each with mg + emoji), tap to log;
   plus a custom add (name + mg) that's saved and reused.
5) "Logged Today": each entry with its peak / half-gone / cleared times + delete.

Persist to localStorage keys caf:logs and caf:custom, and cloud-sync them with
initCloudSync({ appKey:'caffeine', syncedKeys:['caf:logs','caf:custom'], onApplied: rerender }).
