# Feature Spec: In-Plugin Knob Feedback

**Status:** Future — target next beta release after telemetry redesign  
**Priority:** Medium  
**Depends on:** Telemetry redesign (session-based snapshots + retention policy) must ship first

---

## Objective

Let users leave feedback on any parameter directly from within the plugin, at the exact moment they're frustrated or curious about it. The feedback is tied to the param name and its current value, so you always know the context without asking.

This replaces "send an email / post in Discord" with a zero-friction path from frustration to data.

---

## User Flow

1. User right-clicks any knob/slider in the plugin.
2. Existing JUCE context menu appears with a new item at the bottom: **"Leave feedback on this knob"**.
3. An overlay dialog appears inside the plugin window — semi-transparent backdrop, plugin UI still visible behind it.
4. Dialog shows:
   - Label: `Feedback — [Human-readable param name]` (e.g. "Feedback — Drive")
   - Current value displayed in small text (e.g. "Current: 6.2 dB")
   - Text area: placeholder "What's bugging you? What would you change?"
   - Two buttons: **Send** and **Cancel**
5. On Send: fires a single POST to Supabase `parameter_feedback` table, overlay closes.
6. On Cancel or click-outside: overlay closes, no data sent.
7. A brief non-blocking confirmation ("Thanks for the feedback") fades in and out in the plugin header area — no modal, no second click required.

---

## JUCE Implementation

### 1. LookAndFeel extension — right-click menu item

Override `getSliderPopupMenuItems` (or intercept via `mouseDown` with `isRightButtonDown()` check) in the shared `LookAndFeel` subclass used across the suite. Append:

```cpp
PopupMenu::Item feedbackItem;
feedbackItem.itemID   = kFeedbackMenuItemID;   // reserved constant, e.g. 9001
feedbackItem.text     = "Leave feedback on this knob";
feedbackItem.isEnabled = true;
```

The item ID is detected in the `SliderPopupMenuResult` callback. At that point the calling slider is known — extract its `juce::RangedAudioParameter*` from the APVTS to get `paramID` and `getValue()`.

### 2. FeedbackOverlay component

New `FeedbackOverlay : public juce::Component` in `Plugins/Shared/`:

```
FeedbackOverlay.h
FeedbackOverlay.cpp
```

Responsibilities:
- Owns a `juce::TextEditor` (multi-line, 4 rows)
- Owns Send and Cancel `juce::TextButton`
- Draws a semi-transparent dark backdrop (`fillAll` with alpha ~0.75)
- Sized to ~360×200 px, centred in the plugin window
- Constructed with `paramID`, `paramDisplayName`, `normalisedValue`, and a callback lambda for Submit

**Keyboard focus:** `setWantsKeyboardFocus(true)` on the TextEditor; grab focus immediately on show. This is the main cross-host risk — see Testing section.

### 3. Wiring into each PluginEditor

Each plugin's `PluginEditor` holds a `std::unique_ptr<FeedbackOverlay> feedbackOverlay` (null until triggered). On right-click menu result:

```cpp
feedbackOverlay = std::make_unique<FeedbackOverlay>(
    paramID, paramDisplayName, normalisedValue,
    [this](const juce::String& text) { submitFeedback(paramID, normalisedValue, text); }
);
addAndMakeVisible(*feedbackOverlay);
feedbackOverlay->setBounds(getLocalBounds());  // covers full plugin area
feedbackOverlay->grabKeyboardFocus();
```

On submit or cancel: `feedbackOverlay.reset()`.

### 4. Confirmation toast

A `ToastLabel` component (simple `juce::Label` subclass) owned by `PluginEditor`. On successful send, set its text to "Feedback sent — thank you", make visible, start a 2-second `juce::Timer` that fades alpha 1.0 → 0.0 then hides. Positioned in a non-intrusive corner of the plugin header.

---

## Backend

### New Supabase table: `parameter_feedback`

```sql
create table parameter_feedback (
    id             uuid primary key default gen_random_uuid(),
    created_at     timestamptz not null default now(),
    plugin_name    text not null,
    plugin_version text not null,
    device_id      text not null,
    session_id     text not null,
    param_id       text not null,
    param_value    real not null,   -- normalised 0..1
    feedback_text  text not null check (char_length(feedback_text) <= 1000)
);

-- Insert-only for anon key — same pattern as parameter_snapshots
alter table parameter_feedback enable row level security;

create policy "anon_insert"
  on parameter_feedback
  for insert
  to anon
  with check (true);

-- No select/update/delete for anon key — service role only for reads
```

Add to `supabase/` as `11_parameter_feedback.sql`.

### ScionaughTelemetry — new method

Add to `ScionaughTelemetry.h/.cpp`:

```cpp
// Call from message thread only (UI callback).
void sendFeedback(const juce::String& paramID,
                  float normalisedValue,
                  const juce::String& feedbackText);
```

Implementation mirrors `sendSnapshot()` — builds a JSON object, fires on `networkThread`. Targets `parameter_feedback` endpoint. Respects the existing consent check.

No timer involved — purely event-driven. This is the exact pattern telemetry should adopt more broadly.

---

## What to NOT do

- Do not open a native OS dialog (`AlertWindow::showMessageBox` async is fine for errors, but not the main feedback entry point — native dialogs behave unpredictably in VST windows on some hosts).
- Do not block the audio thread — all UI and network work on message/network threads only.
- Do not add feedback to every component type — knobs and sliders only for now. Not menu items, toggles, or labels.
- Do not send feedback without text — validate `feedbackText.trim().isNotEmpty()` before enabling Send button.

---

## Cross-Host Testing Required Before Ship

The overlay keyboard focus is the main risk. Test text input in:
- Ableton Live (AU + VST3, Mac)
- Logic Pro (AU, Mac)
- Reaper (VST3, Mac + Windows)
- Pro Tools (AAX — if/when supported)

Specifically verify: typing into the TextEditor doesn't trigger DAW keyboard shortcuts (Ableton is the known problem case for this — it intercepts keys even when a plugin window has focus).

**Mitigation if typing is unreliable in a host:** fall back to a "tap to type" mode using `juce::SystemClipboard` — user types elsewhere, pastes. Not ideal but avoids the focus issue entirely.

---

## Files Affected

| File | Change |
|------|--------|
| `Plugins/Shared/FeedbackOverlay.h` | New |
| `Plugins/Shared/FeedbackOverlay.cpp` | New |
| `Plugins/Shared/ScionaughTelemetry.h` | Add `sendFeedback()` declaration |
| `Plugins/Shared/ScionaughTelemetry.cpp` | Implement `sendFeedback()` |
| `Plugins/Shared/LookAndFeel.h/.cpp` | Add right-click menu item (if shared LF exists), or per-plugin if not |
| `Plugins/Scionaugh*/Source/PluginEditor.h` | Add `feedbackOverlay` + `toastLabel` members (×4) |
| `Plugins/Scionaugh*/Source/PluginEditor.cpp` | Wire right-click result → overlay (×4) |
| `supabase/11_parameter_feedback.sql` | New — run against both telemetry Supabase project |
