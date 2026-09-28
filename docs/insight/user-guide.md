# Clinical operator guide: InSight cataract screening

Welcome to the InSight clinical decision support system (CDSS). This guide provides step-by-step instructions for clinical operators, triage nurses, and healthcare assistants to perform rapid cataract screenings, review AI-generated visual evidence, and generate patient referral documentation.

**Audience:** Clinical operators, triage nurses, and healthcare assistants.

!!! warning "Important clinical disclaimer"
    InSight is an automated decision support tool designed to assist in triage prioritization in resource-constrained environments. It does not provide a definitive medical diagnosis and should never replace evaluation by a licensed ophthalmologist or eye care specialist.

## Sign in to the system

Before you screen any patients, confirm that you have valid clinical credentials and that you know your assigned role, since available features depend on it.

1. Open your web browser and go to the clinical portal URL (for example, `https://insight-screening.internal` or `http://localhost:8501`).
2. On the welcome screen, enter your clinical credentials (email and password).
3. Select **Log In**.
    * **Note:** Your user role (**Nurse** or **Doctor**) determines which features are available. If you need access to the analytics dashboard, contact your system administrator.

## Prepare retinal fundus scans

To ensure accurate screening and prevent processing errors, fundus photographs must meet the following intake standards:

* **Supported formats:** `.jpg`, `.jpeg`, or `.png`.
* **Image quality:** The pupil is centered, and the lens is free of external glare or eyelid obstruction.
* **Validation check (the gatekeeper):** The system automatically checks incoming files. If you upload a non-retinal image, such as a facial photo, a document, or an object, the system rejects the file and shows an `Invalid Image Type` notification.

## Screen a single patient

After a scan meets the intake standards, screen it using the following workflow.

1. In the left sidebar, go to **Patient Screening** > **Single Scan**.
2. In the **Patient Identifier** field, enter the assigned patient ID (for example, `PAT-2026-0841`). Don't enter personal details such as names or national identity numbers.
3. Select **Browse Files**, or drag and drop the file, to upload the retinal fundus image.
4. Select **Run Screening Analysis**.
5. After processing completes (typically 3–5 seconds), review the screening card:
    * **Classification:** Shows either **Normal** (no significant cataract indications) or **Cataract Detected**.
    * **Confidence level:** Shows a percentage that indicates the model's certainty.

## Read the Grad-CAM heatmap

When InSight classifies an image, it provides an explainable visual overlay called a Grad-CAM saliency map. This map helps you verify why the model flagged a scan.

| Heatmap color | Clinical meaning | Recommended action |
| :--- | :--- | :--- |
| 🔴 Deep red / orange | Area of highest visual importance to the AI, typically localized lens opacity or clouding. | Visually inspect this exact anatomical region for cataract signs. |
| 🟡 Yellow / green | Moderate influence on the screening decision. | Treat as a secondary area of interest. |
| 🔵 Blue / violet | Minimal to no influence; normal ocular structures. | No action needed; no abnormalities are flagged here. |

!!! tip "Clinical tip"
    If the model flags **Cataract Detected**, but the red heatmap highlights an eyelash, an eyelid shadow, or a camera artifact rather than the lens or pupil, treat the result as inconclusive and recapture the scan.

## Export a referral report as PDF

If a patient needs follow-up care with an ophthalmology specialist, export a referral report:

1. On the completed screening result screen, click **Generate Referral Report (PDF)**.
2. The system compiles a one-page PDF that contains the following:
    * The patient identifier and screening timestamp.
    * A side-by-side display of the original fundus scan and the Grad-CAM heatmap.
    * The model's classification score and the standard clinical disclaimer.
3. Select **Download PDF** to save the document to your local clinic records, or attach it to the patient's electronic referral file.

## Troubleshoot common intake issues

| Problem | Probable cause | Corrective action |
| :--- | :--- | :--- |
| `Invalid Image Type` error banner | The image failed the pre-validation gatekeeper check, for example, a blurry scan or a non-fundus picture. | Confirm that the file is a clean fundus scan of the eye, not an external camera photo. |
| `File Size Exceeded` alert | The scan resolution exceeds the maximum upload limit, typically 10 MB. | Compress or resize the image before you upload it. |
| Processing spinner freezes | A temporary network interruption occurred between the terminal and the backend service. | Refresh your browser window and retry the upload. If the issue persists, contact IT support. |

## Related resources

* [Developer and API reference](developer-guide.md): Endpoint specifications, request and response formats, and integration details.
* IT support: Contact your system administrator for access, credential, or connectivity issues.