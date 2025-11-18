# Data Versioning with DVC and Git

This repository demonstrates a foundational workflow for managing and versioning data files using **Data Version Control (DVC)** alongside **Git**. This setup allows the data (which can be large) to be stored separately (in a remote storage, simulated here by an `S3` directory) while Git tracks lightweight metadata files (like `data.dvc`) pointing to the specific data versions.

---

## 🚀 Project Workflow Summary

[cite_start]The project workflow involved three main stages to create three distinct versions of the `data/sample_data.csv` file, each version tracked and pushed separately via DVC and Git[cite: 5].

### Data Generation (`mycode.py`)

The `mycode.py` script uses the pandas library to generate a DataFrame and save it as a CSV file in a dedicated `data/` directory. The script was executed multiple times, with modifications, to create `V1`, `V2`, and `V3` of the data.

| Version | Description | Row Count |
| :--- | :--- | :--- |
| **V1** | Initial data save. | 3 |
| **V2** | Added one new row (`GF1`). | 4 |
| **V3** | Added a second new row (`GF2`). | 5 |

---

## ⚙️ Key Setup and DVC Commands

### 1. Initial Setup and Git Tracking Stop

[cite_start]Initially, the project code was committed, and the `data/` folder was being tracked by Git[cite: 3].

1.  **Initialize DVC and Remote:**
    ```bash
    [cite_start]pip install dvc # Install DVC [cite: 3]
    [cite_start]dvc init        # Initialize DVC [cite: 4]
    [cite_start]mkdir S3        # Create a local directory for remote simulation [cite: 5]
    [cite_start]dvc remote add -d myremote S3 # Set S3 as the default remote storage [cite: 6]
    ```

2.  **Add Data to DVC and Remove from Git:**
    [cite_start]The `data/` directory was added to DVC[cite: 7]. [cite_start]Because it was previously tracked by Git, it had to be removed from Git's tracking[cite: 7]:
    ```bash
    [cite_start]dvc add data/ # Start tracking with DVC [cite: 7]
    [cite_start]git rm -r --cached 'data' # Stop tracking data/ with Git [cite: 7]
    [cite_start]git commit -m "stop tracking data" # Commit the removal [cite: 7]
    dvc add data/ # Run again to finalize data.dvc creation
    git add .gitignore data.dvc # Add the DVC metadata file
    ```

### 2. Versioning Data (V1, V2, V3)

After the initial setup, every change to the data involves three steps: `dvc commit`, `dvc push`, and a final `git add/commit/push` of the updated `data.dvc` file.

| Action | DVC Commands | Git Commands |
| :--- | :--- | :--- |
| **V1 (Initial)** | `dvc commit` <br> `dvc push` | `git add/commit/push` |
| **V2 (Change)** | `dvc commit` <br> `dvc push` | `git add data.dvc` <br> `git commit/push` |
| **V3 (Change)** | `dvc commit` <br> `dvc push` | `git add data.dvc` <br> `git commit/push` |

### 3. Key Metadata File (`data.dvc`)

The `data.dvc` file is a lightweight file tracked by Git. [cite_start]Its contents contain a unique hash (`md5`) that points to the data version stored in the DVC remote (`S3` folder)[cite: 7].

```yaml
outs:
- md5: 0377810b244c0b941080f10326f6b995.dir
  size: 102
  nfiles: 1
  hash: md5
  path: data