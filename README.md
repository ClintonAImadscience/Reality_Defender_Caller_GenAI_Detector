# Reality_Defender_Caller_GenAI_Detector
This is a simple notebook for users to upload an audio or video file and make an API call to Reality Defender's AI detection ...AI - returns confidence score in 'manipulated' or 'authentic'



Reality Defender Audio Check — Minimal Colab PoC

This notebook sends a single audio file to the Reality Defender API and returns a clear decision with model‑level scores. It uses the asynchronous API flow, shows visible progress while waiting, enforces a total timeout to avoid hanging cells, and validates your file before any API call to avoid wasting trial quota.

What this notebook does

Lets you upload one audio file and validates it locally first:

Allowed extensions: flac, wav, mp3, m4a, aac, alac, ogg

Maximum size: 20 MB (free trial)

Submits the file using the Reality Defender Python SDK.

Polls for results in short “slices” so you see progress and the cell never appears frozen.

Prints a concise summary: overall status and score, plus per‑model decisions/scores.

Optionally saves the full JSON response to a timestamped file in /content.

Why this approach

The SDK’s synchronous method (detect_file) can block without feedback in notebooks. The async path gives control, progress, and a hard timeout.

Local validation prevents unnecessary API calls if the file is too large or has an unsupported type, protecting your monthly trial limit.

The output is intentionally minimal and readable for quick spot checks.

Quick start (cells/blocks)

P1 — Install SDK and Imports
Installs realitydefender==0.1.9, enables asyncio in Colab, and verifies the installed SDK version.

P2 — API Key (safe input)
Reads REALITY_DEFENDER_API_KEY from the environment, or securely prompts once and stores it in this runtime.

P3 — Upload + Validate
Upload a single file and validate size/type before any API call. If validation fails, the cell exits with a clear message and no API usage.

P4 — Submit + Poll (async)
Calls upload() to get a request_id, then polls get_result(request_id) in timed slices:

Default total timeout: 360 seconds.

Default slice length: 20–30 seconds (dots indicate continued progress).

If the total timeout fires, re‑run P4 to continue waiting using the same request_id without re‑uploading.

P5 — Save Result (optional)
Saves the full JSON to /content/rd_result_<timestamp>.json for later inspection or comparison.

Interpreting results

Overall status: Reality Defender’s top‑level decision (for example, AUTHENTIC, MANIPULATED, or INCONCLUSIVE).

Overall score: A normalized confidence value in [0.0, 1.0] (higher typically means stronger confidence).

Models: Each audio detector returns its own status and score. Models may disagree; the ensemble model provides a combined view.

Example output (from a synthetic ElevenLabs sample):

Overall status: MANIPULATED
Overall score:  0.990
Models:
  - rd-everest-aud: MANIPULATED (score=0.990)
  - rd-slim-aud: MANIPULATED (score=0.990)
  - rd-aud-ensemble: MANIPULATED (score=0.990)

Expectations and limitations

Processing time varies with file length, system load, and account tier. The notebook enforces a total timeout to keep cells responsive; if you hit it, re‑run the polling cell to continue waiting.

Decision thresholds are policy‑dependent. This PoC surfaces scores and decisions but does not impose a pass/fail threshold.

This is a single‑file, analyst‑oriented workflow. Batch processing, dashboards, and persistence layers are intentionally out of scope.

Troubleshooting

Import error or version mismatch
Re‑run Block P1 or restart the runtime, then run P1 → P4 in order.

Unauthorized
Make sure the API key is valid and re‑enter it in Block P2.

Validation fails
Check the size and file extension in Block P3.

Timeout while polling
Increase the total timeout in Block P4 (for example, to 480 seconds) or re‑run Block P4 to continue waiting with the same request_id.

Notes for engineers

SDK: Python package realitydefender==0.1.9.

Flow: upload(file_path) -> request_id then get_result(request_id) (SDK handles internal polling).

Scores: normalized to [0.0, 1.0]. The per‑model array includes name, status, and score.

The code avoids detect_file() to retain progress visibility and control in notebooks.
