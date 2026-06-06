





Add a settings panel to my dashboard homepage (index.html, the bento hub). Match our
design exactly: same :root tokens, fonts, glass styling. When done, commit and push to GitHub.

GEAR BUTTON: put a gear (cog) icon button in the top-right of the page header, on the same
row as the "Dashboard" title (wrap the title + button in a flex row). 42x42, rounded 12px,
faint border, subtle hover; the cog nudges/rotates slightly on press.

MODAL: clicking the gear opens a centered "Your Data" modal over a blurred dark backdrop.
Use class .modal-bg (toggle .show to open) and an inner .modal so it reuses topbar.js's
mobile-fullscreen + scroll-lock behavior. Close on the × button, on backdrop click, and
on Escape.

FIELDS (pre-fill from saved data on open):
- Height — number input with a [cm | ft/in] segmented toggle; ft/in shows two inputs (ft, in).
- Weight — number input with a [kg | lb] segmented toggle.
- Age — number input.
- Sex — [Male | Female] segmented toggle.
- Active hours / week — number input.
- A primary "Save" button with a "Saved ✓" confirmation that fades after ~1.8s.

STORAGE: save into the SHARED po_water_v1 object so the Water and Caffeine pages use it.
Load existing po_water_v1 (or a default that mirrors topbar.js's defaultWaterState) and
MERGE — never overwrite logs. Write profile.weightKg (convert lb→kg) + top-level weightUnit,
profile.heightCm (convert ft/in→cm) + profile.heightUnit, profile.age, profile.sex,
profile.activityHrsPerWeek. Unit toggles should convert the currently shown value in place.

WHOOP CARD (below a divider): read whoop_tokens_v1.
- If connected: green dot + "Connected", a "Last synced X min ago" line (from a
  whoop_last_sync timestamp), and three stats — Recovery %, Sleep %, Rest HR. A "Sync now"
  button fetches /recovery and /activity/sleep via /api/whoop-data (refresh via
  /api/whoop-refresh on 401), updates the stats, and stamps whoop_last_sync. Auto-sync once
  when the panel opens.
- If not connected: grey dot + "Not connected" and the button reads "Connect on Health page"
  and links to health.html.
