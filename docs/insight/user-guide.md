# Clinical Operator Guide: InSight Cataract Screening

Welcome to the **InSight Clinical Decision Support System (CDSS)**. This guide provides step-by-step instructions for clinical operators, triage nurses, and healthcare assistants to perform rapid cataract screenings, review AI-generated visual evidence, and generate patient referral documentation.

**Audience:** Clinical operators, triage nurses, and healthcare assistants.

!!! warning "Important Clinical Disclaimer"
    InSight is an automated **decision-support tool** designed to assist in triage prioritization in resource-constrained environments. It does **not** provide a definitive medical diagnosis and should never replace evaluation by a licensed ophthalmologist or eye-care specialist.

## System Access & Login

Before screening any patients, confirm you have valid clinical credentials and know your assigned role, since available features depend on it.

1. Open your web browser and navigate to the clinical portal URL (e.g., `https://insight-screening.internal` or `http://localhost:8501`).
2. On the welcome screen, enter your clinical credentials (email and password).
3. Select **Log In**.
    * **Note:** Your user role (**Nurse** or **Doctor**) determines available features. If you require access to the Analytics Dashboard, contact your system administrator.

## Preparing Retinal Fundus Scans

To ensure accurate screening and prevent processing errors, fundus photographs must meet the following intake standards:

* **Supported Formats:** `.jpg`, `.jpeg`, or `.png`.
* **Image Quality:** Ensure the pupil is adequately centered and the lens is clear of external glare or eyelid obstruction.
* **Validation Check (The Gatekeeper):** The system automatically checks incoming files. If a non-retinal image (such as an external facial photo, document, or object) is uploaded, the system will reject the file with an `Invalid Image Type` notification.

## Screening a Single Patient

Once a scan meets intake standards, run it through the screening workflow below.

1. From the left sidebar, navigate to **Patient Screening** > **Single Scan**.
2. In the **Patient Identifier** field, enter the assigned patient ID (e.g., `PAT-2026-0841`). Do not enter sensitive personal details such as names or national identity numbers.
3. Select **Browse Files** (or drag and drop) to upload the retinal fundus image.
4. Select **Run Screening Analysis**.
5. Once processing completes (typically 3–5 seconds), review the screening card:
    * **Classification:** Displays either **Normal** (no significant cataract indications) or **Cataract Detected**.
    * **Confidence Level:** Expressed as a percentage indicating the model's certainty.

## Reading the Grad-CAM Visual Heatmap

When an image is classified, InSight provides an explainable visual overlay called a **Grad-CAM Saliency Map**. This helps clinicians verify why the model flagged a scan.

| Heatmap Color Indicator | Clinical Meaning | Recommended Action |
| :--- | :--- | :--- |
| 🔴 **Deep Red / Orange** | Area of **highest visual importance** to the AI (typically localized lens opacity or clouding). | Visually inspect this exact anatomical region for cataract signs. |
| 🟡 **Yellow / Green** | Moderate influence on the screening decision. | Secondary area of interest. |
| 🔵 **Blue / Violet** | Minimal to no influence; normal ocular structures. | No significant abnormalities flagged here. |

!!! tip "Clinical Tip"
    If the model flags **Cataract Detected** but the red heatmap highlights an eyelash, eyelid shadow, or camera artifact rather than the lens/pupil, treat the result as inconclusive and re-capture the scan.

## Exporting a Clinical Referral Report (PDF)

If a patient requires follow-up care with an ophthalmology specialist:

1. On the completed screening result screen, click **Generate Referral Report (PDF)**.
2. The system automatically compiles an official one-page PDF document containing:
    * The Patient Identifier and screening timestamp.
    * Side-by-side display of the original fundus scan and the Grad-CAM heatmap.
    * Model classification score and standard clinical disclaimer.
3. Select **Download PDF** to save the document to your local clinic records or attach it to the patient's electronic referral file.

## Troubleshooting Common Intake Issues

| Problem | Probable Cause | Corrective Action |
| :--- | :--- | :--- |
| **"Invalid Image Type" Error Banner** | The image failed the pre-validation gatekeeper (e.g., blurry scan, non-fundus picture). | Ensure the file is a clean fundus scan of the eye, not an external camera photo. |
| **"File Size Exceeded" Alert** | Scan resolution exceeds the maximum allowed upload limit (typically >10 MB). | Compress or resize the image before uploading. |
| **Processing Spinner Freezes** | Temporary network interruption between the terminal and the backend service. | Refresh your browser window and retry the upload. If the issue persists, contact IT support. |

## Related Resources

* [Developer & API Reference](developer-guide.md) — endpoint specs, request/response formats, and integration details.
* IT Support — contact your system administrator for access, credential, or connectivity issues.