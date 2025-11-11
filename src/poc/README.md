# MCP Server Tools

This document describes the tools available in the MCP (Model Context Protocol) server for managing and maintaining Tomcat application servers.

## Overview

The `main.py` script implements a `FastMCP` server that exposes several tools for interacting with Tomcat instances. These tools can be used to get information, analyze logs, check for vulnerabilities, and update the Tomcat server.

## Available Tools

Here is a list of the available tools and their functions:

### 1. `get_tomcat_version`

-   **Description:** Gets the current Tomcat version running on a given application server.
-   **Arguments:**
    -   `application`: The name of the application (e.g., `test-application-1`).
-   **Returns:** The Tomcat version string (e.g., `9.0.100`).

### 2. `summarize_tomcat_logs`

-   **Description:** Summarizes Tomcat logs for a specific day. It can help identify exceptions, warnings, or severe log entries.
-   **Arguments:**
    -   `application`: The name of the application.
    -   `day`: The day for which to gather logs. This can be "today", "yesterday", "N days ago", or a date in "YYYY-MM-DD" format.
-   **Returns:** A summary of the log file content.

### 3. `scan_tomcat_libs`

-   **Description:** Lists the JAR files inside Tomcat's `lib` directory and can be used to check them for vulnerabilities.
-   **Arguments:**
    -   `application`: The name of the application.
-   **Returns:** A list of JAR files and their versions.

### 4. `check_tomcat_vulnerabilities`

-   **Description:** Checks a given Tomcat version for known CVEs (Common Vulnerabilities and Exposures) using the NVD API.
-   **Arguments:**
    -   `version`: The Tomcat version to check.
-   **Returns:** A list of CVEs found for the given version, or a message indicating that no CVEs were found.

### 5. `update_tomcat_version`

-   **Description:** Launches an Ansible AWX job to update the Tomcat version on a specified application server.
-   **Arguments:**
    -   `version`: The target Tomcat version.
    -   `application`: The application server to update.
-   **Returns:** The status of the AWX job.

## Usage

The MCP server can be run from the command line or built in to an MCP client. The tools can then be called via the configured transport (e.g., HTTP or stdio).

```bash
uv --directory /path/to/mcp-system-poc/src/poc run main.py
```