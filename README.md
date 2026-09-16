# hs-form-tracking

Reusable snippets and a Google Tag Manager (GTM) container export for pushing HubSpot form submission data into `dataLayer`, so it can be picked up by GTM tags (GA4, ads conversion tags, etc.).

## What's in here

| File | Purpose |
|------|---------|
| `hs-form-submission.html` | Standalone `<script>` snippet that listens for HubSpot form submissions (both legacy iframe forms and v4 forms) and pushes a `form_submit` event to `window.dataLayer`. |
| `gtm-hubspot-form-tracking.json` | A GTM container export implementing the same listener as a Custom HTML tag, plus a `HubSpot Form ID Type Lookup` variable and example triggers that fire on specific form submissions. |

## How it works

1. A listener tag fires on all pages and subscribes to HubSpot's form events (`message` postMessage for legacy embeds, `hs-form-event:on-submission:success` for v4 embeds).
2. On submission, it pushes a `form_submit` event onto `dataLayer` along with the HubSpot form GUID (`hs-form-guid`) and any submitted `email`/`phone` values.
3. A Data Layer Variable (`hs-form-guid`) reads the form GUID back out of the event.
4. A Lookup Table variable (`HubSpot Form ID Type Lookup`) maps each form's GUID to a human-readable form type (e.g. "Demo Request", "Content Download").
5. Downstream triggers fire on specific form types by checking `{{HubSpot Form ID Type Lookup}}`, letting you build per-form-type tags (conversion tracking, ads pixels, notifications, etc.) without hardcoding GUIDs into every trigger.

## Setup

1. Import `gtm-hubspot-form-tracking.json` into a GTM workspace (**Admin → Import Container**).
2. Open the `HubSpot Form ID Type Lookup` variable and fill in the `key` column for each row with the actual HubSpot form GUID from your portal (found in HubSpot under **Marketing → Forms → [your form] → Options**, or via the Forms API). The `value` column already has example labels you can rename or extend.
3. Add/duplicate the example triggers (`HS Form Submission - Demo Request`, etc.) for whichever form types you need to react to downstream.
4. Publish the container.

If you're not using GTM, drop `hs-form-submission.html` directly onto your pages instead — it pushes the same `dataLayer` event without any GTM dependency.

## Notes

- The GTM export has been sanitized for public sharing: account ID, container ID, container name, and the HubSpot form GUIDs have been replaced with placeholders. Re-add your own values after importing.
- Tag/variable/trigger names use generic naming (`HubSpot Form Listener`, `hs-form-guid`, `HS Form Submission - <type>`) so the container can be reused across portals — rename as needed to fit your own naming conventions.
