# Lift Vision Lab

A browser-only computer vision prototype for counting occupied seats on departing chairs. Download or clone this repository and open `index.html` in desktop Chrome or Edge. No installation, server, model download, or account is required.

## Run the portfolio demo

1. Select **Baseline**, then **Run sample to end**. The 36-second scene contains six chairs, including an empty chair. The 10-second rider totals are 5, 2, 3, and 2.
2. Select **Snow, occlusion and low contrast** and rerun. Expect totals of 4, 2, 2, and 2. An occlusion and a low-contrast rider each cause one miss.
3. Select an event time to inspect its crossing frame. Correct equipment or patrol labels, or reject a false count. The time series updates immediately. Raw metrics continue to reflect the original detections.
4. Export events, accepted totals, and sample ground truth as CSV before switching clips or closing the page. Optionally enter an ISO 8601 recording start with an explicit offset before running to include UTC timestamps. Otherwise the exports use clip-relative seconds. Time bins start at clip time zero and the final bin may be partial.

The detector reads rendered frame pixels, extracts connected dark silhouettes, links detections over time, and counts each track once after it approaches from the left and clears the gate to the right. It uses recent silhouette geometry for sample equipment and red clothing as a sample patrol cue. Track IDs are local to a single run. Repeat rides count as separate events.

| Scenario | Truth | Detected | Precision | Recall | Equipment agreement on matches |
|---|---:|---:|---:|---:|---:|
| Baseline | 12 | 12 | 100% | 100% | 100% |
| Stress | 12 | 10 | 100% | 83.3% | 100% |

Sample evaluation matches events one-to-one within 0.5 seconds and 18 vertical pixels. Timing error and patrol cue scores apply to matched riders. One missed stress rider carried a patrol cue, giving end-to-end stress patrol recall of 1 of 2. These results apply to synthetic footage. The counting and scoring logic was checked with simulated canvas frames. Browser playback and controls still need an end-to-end check.

## Try a local clip

Use an authorized, browser-playable, pre-cropped shot from a fixed camera. Place the departure gate at the horizontal midpoint and arrange uphill motion from left to right. Drag vertically on the preview before opening the clip to restrict the analysis band. The demo scales frames to 480 × 220 and uses a fixed brightness threshold. Dark hardware, overlapping riders, low contrast, camera movement, and dropped playback frames can affect the counts.

Local equipment and patrol fields start as unknown for manual review. Use the original video to inspect audit timestamps and keep an independent ground-truth CSV to record misses. Reopening a clip starts a fresh run. Use disjoint clip intervals and separate run folders or add a clip ID before combining exports.

The demo processes local frames in the browser without network requests or persistent browser storage. It uses short-lived tracks without face recognition or cross-run identities. CSVs are exported on request. For a portfolio recording, show both sample scenarios, an audit correction, and exported totals.

## Resort pilot for the 2026–27 ski season

### 1. Site walk and sign-off before opening

Have lift operations and the resort's privacy lead approve mounting, field of view, signage, access, and retention. Count one occupied seat crossing the uphill departure line after loading finishes. Keep the queue, returning chairs, and bystanders outside the region of interest.

Try a side-oblique or slightly elevated drive-building view that separates seats and shows gear against snow. Compare two authorized candidate views for occlusion, rider detail, glare, shadows, and falling snow. Start with 1080p at 25–30 fps if available and use the smallest crop and resolution that preserves visibility. Confirm safe mounting, winter power, enclosure, clock synchronization, and maintenance access. Log framing changes and outages.

### 2. Collect and label early-season footage

Collect short approved clips across quiet and busy periods, weather and lighting, stops and restarts, and empty, partial, and full chairs. Label each departure and each occupied seat with gate time, chair and seat index, equipment, cue status, and visibility. Keep skier, snowboarder, adaptive sit-ski, other, and unknown distinct in field labels. Use unknown when evidence is insufficient. The demo groups unsupported equipment into unknown.

Have a second reviewer independently label a representative subset and uncertain or rare cases. Reconcile disagreements before scoring. Split development and held-out footage by recording session and report sample sizes by category and condition.

### 3. Fit the detector and evaluate offline

Start with seat occupancy. Use chair geometry and seat position to constrain tracking where riders overlap. If the pixel baseline fails, try an on-device rider detector or a chair detector followed by per-seat occupancy. Use a stable chair-pass and seat key to deduplicate across retries and files. Preserve UTC source times and record capture coverage so outages can be separated from empty periods.

Match by chair and seat where possible using a preset timing tolerance. Report event precision and recall, false counts and misses, per-chair occupancy error, matched timing error, and 15-minute absolute error and signed bias. Break out weather, lighting, and occupancy. For equipment, report a confusion matrix, per-class precision and recall, unknown rate, and sample sizes. Score patrol cues separately and include missed riders in end-to-end recall. Keep patrol observations aggregate.

Proposed starting gates for resort review are 95% event precision and recall overall, 90% recall in each well-sampled condition, and 15-minute absolute error within two riders or 5% of the manual count, whichever is larger. Keep equipment and patrol estimates experimental until enough independently reviewed examples support class-specific targets. Rare adaptive categories can remain manual.

### 4. Run a supervised shadow pilot

Run alongside manual spot counts over representative shifts. Review missed and false counts, coverage gaps, and framing changes daily. Confirm reliable capture, aggregate reconciliation, and scheduled deletion before expanding. Log the software version and camera calibration for each run.

Crop at capture or on-device before saving, disable audio, restrict access to raw video and event exports, and delete footage after the approved review window. Keep aggregates separate from source footage. Use coarser public time bins or suppress small cells to reduce the risk of singling people out. Publish synthetic footage and resort-approved aggregate results only.
