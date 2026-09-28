# InSight developer and API reference guide

This guide provides technical specifications for integrating with and extending the InSight clinical decision support microservice. It covers environment configuration, request contracts, database side effects, and response schemas for the FastAPI inference backend.

## Service overview and runtime

* **Framework:** FastAPI (Python 3.9 or later)
* **ASGI server:** Uvicorn
* **Base URL:** `http://127.0.0.1:8000`
* **Interactive documentation (Swagger UI):** `http://127.0.0.1:8000/docs`
* **Alternative schema (ReDoc):** `http://127.0.0.1:8000/redoc`

## Authentication and authorization

Clinical terminals and operators pass an authorized `user_id`, a universally unique identifier (UUID) that represents the authenticated doctor or nurse from Supabase Auth, in the multipart form payload:

* **Field:** `user_id` (string / UUID)
* **Access scopes:** Validated against existing Supabase authentication users, to associate patient intakes and prediction logs with specific clinical operators.

## Endpoints

### Cataract screening and intake pipeline

This endpoint processes an ocular fundus scan through the ResNet-18 inference engine, generates a Grad-CAM saliency map, uploads assets to Supabase Storage, and commits audit records to PostgreSQL.

* **Path:** `POST /predict/`
* **Endpoint URL:** `http://127.0.0.1:8000/predict/`
* **Content-Type:** `multipart/form-data`

#### Request parameters

All parameters are required form-data fields:

| Field | Type | Encoding | Required | Description |
| :--- | :--- | :--- | :--- | :--- |
| `file` | Binary | File (`UploadFile`) | Yes | Retinal fundus image (`.jpg`, `.jpeg`, `.png`). |
| `patient_name` | string | Form (`str`) | Yes | Full name or clinical pseudonym of the patient. |
| `age` | integer | Form (`int`) | Yes | Patient age, in years. |
| `gender` | string | Form (`str`) | Yes | Biological sex or gender identifier, for example, `Female`, `Male`, or `Other`. |
| `medical_history` | string | Form (`str`) | Yes | Relevant pre-existing conditions, for example, `Type 2 Diabetes`, `Hypertension`, or `None`. |
| `symptoms` | string | Form (`str`) | Yes | Self-reported visual disturbances, for example, `Cloudy vision` or `Haloes around lights`. |
| `user_id` | string | Form (`str`) | Yes | UUID or account identifier of the screening clinician. |

#### Request examples

=== "cURL"

```bash
    curl -X POST "http://127.0.0.1:8000/predict/" \
      -F "file=@scans/patient_scan_01.png" \
      -F "patient_name=PAT-2026-0841" \
      -F "age=64" \
      -F "gender=Female" \
      -F "medical_history=Type 2 Diabetes" \
      -F "symptoms=Blurry vision in left eye" \
      -F "user_id=usr_9b1deb4d-3b7d-4bad-9bdd-2b0d7b3dcb6d"
```

=== "Python (Requests)"

```python
    import requests

    api_url = "http://127.0.0.1:8000/predict/"

    # Prepare the multipart payload.
    payload = {
        "patient_name": "PAT-2026-0841",
        "age": 64,
        "gender": "Female",
        "medical_history": "Type 2 Diabetes",
        "symptoms": "Blurry vision in left eye",
        "user_id": "usr_9b1deb4d-3b7d-4bad-9bdd-2b0d7b3dcb6d",
    }

    with open("scans/patient_scan_01.png", "rb") as image_file:
        files = {"file": ("patient_scan_01.png", image_file, "image/png")}
        response = requests.post(api_url, data=payload, files=files)

    print(response.status_code)
    print(response.json())
```

## Response schemas and status codes

### `200 OK`: inference and persistence successful

Returned when the scan is classified, the Grad-CAM map is generated, and the patient and prediction records are persisted to Supabase.

```json
{
  "class": "Cataract",
  "confidence": 0.9854,
  "all_probabilities": {
    "No Cataract": 0.0146,
    "Cataract": 0.9854
  },
  "patient_id": "841b8a53-487e-4009-8ff4-938814529db7",
  "image_url": "https://your-project.supabase.co/storage/v1/object/public/scans/patient_scan_01.png",
  "gradcam_url": "https://your-project.supabase.co/storage/v1/object/public/scans/gradcam_4f09d82e-68be-4091-8d26-a07727e4eef6.png"
}
```

#### Response field reference

| Key | Type | Description |
| :--- | :--- | :--- |
| `class` | string | Final categorical classification: `"Cataract"` or `"No Cataract"`. |
| `confidence` | float | Softmax probability assigned to the predicted class (0.0 to 1.0). |
| `all_probabilities` | object | Complete probability distribution across both diagnostic classes. |
| `patient_id` | string / int | Generated primary key record ID from the `patients` table in Supabase. |
| `image_url` | string | Public or pre-signed URL of the raw uploaded fundus photograph in cloud storage. |
| `gradcam_url` | string | Public or pre-signed URL of the generated Grad-CAM saliency visualization in cloud storage. |

### `422 Unprocessable Entity`: missing or invalid payload

FastAPI returns this automatically if required form fields are missing, or if field data types don't match, for example, if you pass a string for `age`.

```json
{
  "detail": [
    {
      "loc": ["body", "age"],
      "msg": "value is not a valid integer",
      "type": "type_error.integer"
    }
  ]
}
```

### `500 Internal Server Error`: pipeline exception

Returned if an unhandled exception occurs during tensor transformation, the model forward pass, or the Supabase storage upload.

```json
{
  "detail": "Failed to upload image to Supabase: Bucket not found or connection timed out."
}
```

!!! warning "Retry guidance"
    A `500` response indicates a server-side or infrastructure failure, not a client payload error. Retrying an identical request doesn't succeed if the underlying issue, such as a storage bucket misconfiguration, persists. Escalate to IT support if repeated `500` responses occur.

## Database side effects

Every successful call to `POST /predict/` performs two database transactions:

1. **`patients` table insert:** Records patient demographics, history, symptoms, the raw image URL, and the `created_by` clinician ID.
2. **`predictions` table insert:** Links the new `patient_id` to the classification result, the confidence score, the Grad-CAM asset URL, and the clinician audit trail.

## Related resources

* [Clinical operator guide](user-guide.md): The screening workflow for nurses and clinical staff.
* [InSight overview](insight.md): Architecture, model benchmarks, and clinical context. 