---
name: notebooklm-enterprise-api
description: Guide and endpoints for interacting with the Google NotebookLM Enterprise API. This skill covers creating, retrieving, deleting, sharing, and listing notebooks programmatically.
---

# 📔 NotebookLM Enterprise API Skill

Use this skill when you need to interface with the **NotebookLM Enterprise API** to manage and interact with notebook workspaces programmatically.

---

## 1. API Configuration & Prerequisites
All requests require Google Cloud OAuth2 authentication. The API is hosted on the **Discovery Engine** gateway.

*   **API Endpoint:** `https://ENDPOINT_LOCATION-discoveryengine.googleapis.com/v1alpha/projects/PROJECT_NUMBER/locations/LOCATION/notebooks`
*   **Authorization Header:** `Authorization: Bearer $(gcloud auth print-access-token)`
*   **Content-Type:** `application/json`

---

## 2. API Endpoints Reference

### I. Create a Notebook
Creates a new notebook workspace inside NotebookLM Enterprise.

*   **HTTP Method:** `POST`
*   **Path:** `https://ENDPOINT_LOCATION-discoveryengine.googleapis.com/v1alpha/projects/PROJECT_NUMBER/locations/LOCATION/notebooks`
*   **Payload Template:**
    ```json
    {
      "title": "NOTEBOOK_TITLE"
    }
    ```
*   **Example Response:**
    ```json
    {
      "title": "My Research Notebook",
      "notebookId": "a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d",
      "emoji": "",
      "metadata": {
        "userRole": "PROJECT_ROLE_OWNER",
        "isShared": false,
        "isShareable": true
      },
      "name": "projects/PROJECT_NUMBER/locations/LOCATION/notebooks/a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d"
    }
    ```

---

### II. Retrieve a Notebook
Retrieves details, title, and metadata for a specific notebook.

*   **HTTP Method:** `GET`
*   **Path:** `https://ENDPOINT_LOCATION-discoveryengine.googleapis.com/v1alpha/projects/PROJECT_NUMBER/locations/LOCATION/notebooks/NOTEBOOK_ID`
*   **Example Response:**
    ```json
    {
      "title": "My Research Notebook",
      "notebookId": "a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d",
      "emoji": "",
      "metadata": {
        "userRole": "PROJECT_ROLE_OWNER",
        "isShared": false,
        "isShareable": true,
        "lastViewed": "2026-06-22T14:15:00Z",
        "createTime": "2026-06-22T10:00:00Z"
      },
      "name": "projects/PROJECT_NUMBER/locations/LOCATION/notebooks/a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d"
    }
    ```

---

### III. List Recently Viewed Notebooks
Lists all notebooks recently opened or modified in the user session.

*   **HTTP Method:** `GET`
*   **Path:** `https://ENDPOINT_LOCATION-discoveryengine.googleapis.com/v1alpha/projects/PROJECT_NUMBER/locations/LOCATION/notebooks:listRecentlyViewed`
*   **Example Response:**
    ```json
    {
      "notebooks": [
        {
          "title": "My Research Notebook",
          "notebookId": "a1b2c3d4-e5f6-7a8b-9c0d",
          "metadata": { "userRole": "PROJECT_ROLE_OWNER", "isShared": false, "isShareable": true }
        }
      ]
    }
    ```

---

### IV. Delete a Notebook
Permanently deletes a notebook workspace and all its integrated sources.

*   **HTTP Method:** `DELETE`
*   **Path:** `https://ENDPOINT_LOCATION-discoveryengine.googleapis.com/v1alpha/projects/PROJECT_NUMBER/locations/LOCATION/notebooks/NOTEBOOK_ID`

---

## 3. Web Console Links
To interact with notebooks visually in the browser:
*   **Regional Workspace Console:** `https://notebooklm.cloud.google/LOCATION/?project=PROJECT_NUMBER`
*   **Specific Notebook Console:** `https://notebooklm.cloud.google/LOCATION/notebook/NOTEBOOK_ID?project=PROJECT_NUMBER`
