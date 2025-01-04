# Rundeck Analytics 📊🔄

This Python script, **Rundeck Analytics**, fetches execution data from a Rundeck instance and posts it to an Elasticsearch index. It is designed to provide detailed insights into Rundeck job executions and their return on investment (ROI). The script supports concurrent data retrieval using multi-threading and includes features such as retry mechanisms, ROI data extraction, and bulk posting to Elasticsearch.

---

## Table of Contents
- [Configuration 🔧](#configuration-🔧)
- [Features 🌟](#features-🌟)
- [Usage 🚀](#usage-🚀)
- [Functions Overview 📖](#functions-overview-📖)
  - [Core Functions](#core-functions)
  - [Utility Functions](#utility-functions)
- [Error Handling ⚠️](#error-handling-⚠️)
- [Logging 🔊](#logging-🔊)
- [Dependencies 🧰](#dependencies-🧰)

---

## Configuration 🔧

The script requires several configuration parameters to be set at the beginning of the file:

```python
RUNDECK_BASE_URL = "http://rdse.runbook.pagerduty.cloud"    # Rundeck API base URL
RUNDECK_API_VERSION = "50"                                  # Rundeck API version
RUNDECK_API_TOKEN = "your_rundeck_api_token"                # Rundeck API token
ELASTICSEARCH_URL = "http://sparks.local:9200"              # Elasticsearch base URL
INDEX_NAME = "runbook.automation"                           # Elasticsearch index name
DEBUG_MODE = False                                          # Enable or disable debug logging
THREAD_POOL_SIZE = 10                                       # Number of concurrent threads for API requests
default_timeframe_days = 180                                # Default timeframe for execution data retrieval
BATCH_SIZE = 100                                            # Batch size for bulk posting to Elasticsearch
MAX_RETRIES = 5                                             # Maximum number of retries for failed requests
ROI_FIELDS = ["timesaved", "moneysaved"]                    # ROI fields to extract from Rundeck
EXECUTION_MODE = "executions"                               # Modes: "executions" or "executions_and_roi"
```

### Headers for API Requests

- **Rundeck Headers**:
  ```python
  HEADERS_RUNDECK = {
      "X-Rundeck-Auth-Token": RUNDECK_API_TOKEN,
      "Accept": "application/json",
  }
  ```

- **Elasticsearch Headers**:
  ```python
  HEADERS_ELASTICSEARCH = {
      "Content-Type": "application/json"
  }
  ```

---

## Features 🌟

- **Project and Job Data Extraction**: Fetches detailed information about Rundeck projects and their associated jobs.
- **Execution Data Retrieval**: Gathers execution details, including status, user, duration, and timestamps.
- **ROI Data Analysis**: Supports fetching and posting ROI metrics such as time saved and money saved for each execution.
- **Concurrency Support**: Utilizes `ThreadPoolExecutor` to perform multiple API requests in parallel, speeding up data collection.
- **Retry Mechanism**: Automatically retries failed requests with exponential backoff to handle transient network issues.
- **Bulk Posting to Elasticsearch**: Groups execution data into batches and posts them to Elasticsearch using the `_bulk` API, enhancing performance.
- **Execution Modes**:
  - **`executions`**: Only fetch execution data.
  - **`executions_and_roi`**: Fetch both execution data and ROI metrics.

---

## Usage 🚀

1. Clone the repository or download the script.
2. Modify the configuration section with your Rundeck and Elasticsearch details.
3. Install the required dependencies:
   ```bash
   pip install requests
   ```
4. Run the script:
   ```bash
   python rundeck_to_elasticsearch.py [timeframe_days]
   ```

   - **`timeframe_days`**: (Optional) Number of days of data to fetch. Defaults to `180` days if not provided.

---

## Functions Overview 📖

### Core Functions

#### `get_projects()`
Fetches the list of all projects from Rundeck.

#### `fetch_jobs(project_name)`
Fetches the list of jobs for a given Rundeck project.

#### `fetch_executions(job, project_name, timeframe_days)`
Fetches the executions for a specific job within the given timeframe. Supports pagination and posts data to Elasticsearch in batches.

#### `get_execution_roi_data(execution_id)`
Fetches ROI data for a specific execution and extracts only the specified fields (`ROI_FIELDS`).

#### `post_to_elasticsearch(data)`
Posts execution data to Elasticsearch in bulk using the `_bulk` API endpoint.

### Utility Functions

#### `retry_request(func)`
Decorator that retries a function upon network-related exceptions with exponential backoff.

#### `log_debug(message)`
Logs debug messages if `DEBUG_MODE` is enabled.

#### `format_bulk_data(data)`
Formats data for the Elasticsearch bulk API.

#### `fetch_and_post_rundeck_data(timeframe_days)`
Main function that coordinates fetching projects, jobs, and executions, and posting the data to Elasticsearch.

---

## Error Handling ⚠️

The script includes error handling for network-related issues:
- **Retry Mechanism**: Functions decorated with `@retry_request` will automatically retry failed requests up to `MAX_RETRIES` times with exponential backoff.
- **Error Logging**: Errors encountered during requests or data posting are logged to the console.

---

## Logging 🔊

- Debug logs can be enabled by setting `DEBUG_MODE = True` in the configuration section.
- The `log_debug()` function prints detailed information about API responses and data processing when debug mode is enabled.

---

## Dependencies 🧰

The script requires the following Python libraries:
- `requests`
- `json`
- `datetime`
- `concurrent.futures`
- `sys`
- `time`

To install the required dependencies, run:

```bash
pip install requests
```

---

## License 📚

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

