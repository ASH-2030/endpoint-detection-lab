# Changelog
 
## [Detection Lab] - 2026-10-05

### Added
- Installed and enabled Microsoft Sysinternals Sysmon.
- Verified Sysmon Operational event collection.
- Added three Sigma detection rules:
  - PowerShell controlled execution
  - Scheduled Task creation
  - Windows Command Shell execution
- Performed controlled ATT&CK technique tests for:
  - T1059.001 — PowerShell
  - T1053.005 — Scheduled Task/Job
  - T1059.003 — Windows Command Shell
  - T1082 — System Information Discovery
  - T1016 — System Network Configuration Discovery
  - T1564.001 — Hidden Files and Directories

### Validation
- Validated all three Sigma detection rules successfully.
- Confirmed Sysmon Event ID 1 process-creation telemetry for controlled tests.
- Documented an initial Sigma validation mismatch and corrected the field-extraction logic.
- Tested Sysmon Event ID 11 for file creation and documented that the current configuration did not collect the required event.

### Cleanup
- Removed the temporary scheduled task after validation.
- Removed temporary discovery output files.
- Removed the temporary hidden test file.
- Verified cleanup using PowerShell `Test-Path`.

### Limitations
- The planned isolated Windows VM could not be completed because of insufficient available storage.
- Endpoint validation was therefore limited to harmless tests on the student's own Windows 10 Pro system.
- No real organization, third-party infrastructure, production systems, or external credentials were accessed.
