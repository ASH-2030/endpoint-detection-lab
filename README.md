# Purple-Team Emulation: Artefacts and Detection

## Week 4 — Specialist Advanced Build

### Project Overview

This project demonstrates a controlled purple-team emulation and detection workflow based on MITRE ATT&CK techniques. The objective was to generate observable Windows endpoint telemetry, identify the artefacts left behind by controlled activity, and develop detection logic using Sysmon and Sigma.

The project was designed around a small family-owned tax consultancy scenario, where endpoint monitoring is important because systems may contain sensitive client and financial information.

### Scope

Testing was limited to the student's own Windows 10 Pro system and used only controlled, harmless test activity. No real organization, third-party infrastructure, production systems, or external credentials were accessed.

An isolated Windows virtual machine was initially planned, but the VM could not be completed because of insufficient available storage. Therefore, validation was restricted to low-risk endpoint telemetry tests on the student's own Windows system.

### Tools and Technologies

- Windows 10 Pro
- Microsoft Sysinternals Sysmon
- PowerShell
- Windows Command Shell
- Scheduled Tasks (`schtasks`)
- Sigma detection rules
- MITRE ATT&CK technique mapping

### ATT&CK Techniques Tested

| Technique | ATT&CK ID | Tactic | Validation |
|---|---|---|---|
| PowerShell | T1059.001 | Execution | Tested and detected |
| Scheduled Task/Job | T1053.005 | Persistence | Tested and detected |
| Windows Command Shell | T1059.003 | Execution | Tested and detected |
| System Information Discovery | T1082 | Discovery | Tested and observed |
| System Network Configuration Discovery | T1016 | Discovery | Tested and observed |
| Hidden Files and Directories | T1564.001 | Defense Evasion | Tested and observed |

### Detection Engineering

Three Sigma detection rules were created and validated against Sysmon process-creation telemetry:

1. `powershell-test.yml`
   - Detects the controlled PowerShell test.
   - ATT&CK: T1059.001
   - Validation result: MATCH

2. `scheduled-task-test.yml`
   - Detects controlled scheduled-task creation.
   - ATT&CK: T1053.005
   - Validation result: MATCH

3. `cmd-test.yml`
   - Detects the controlled Windows Command Shell test.
   - ATT&CK: T1059.003
   - Validation result: MATCH

### Sysmon Telemetry

The main telemetry source was the Sysmon Operational log.

The tests produced Sysmon Event ID 1 (Process Create) events containing information such as:

- Process image
- Command line
- Parent process
- Parent command line
- User
- Process ID
- File hash

This telemetry was used to verify that the controlled activity was observable and could be mapped to detection logic.

### Testing Results

- 6 ATT&CK technique scenarios were tested or observed.
- 3 Sigma rules were created.
- 3 Sigma rules produced successful MATCH results.
- A scheduled task was created only for controlled validation and was deleted after testing.
- Temporary test files were removed after validation.
- Cleanup was verified using PowerShell `Test-Path`, returning `False` for the temporary test files.

### Problems Found and Fixed

#### Sigma validation mismatch

The first PowerShell Sigma validation attempt returned `NO MATCH`. The issue was caused by the validation script not correctly extracting the required fields from the Sysmon event message.

The validation logic was corrected to explicitly extract `ParentImage` and `ParentCommandLine`. The corrected test returned:

`SIGMA VALIDATION: MATCH`

#### Sysmon FileCreate limitation

A controlled temporary file-creation test was performed using Sysmon Event ID 11. No matching event was returned because the current Sysmon configuration was not collecting the required FileCreate telemetry.

This limitation was recorded rather than treating the test as successful.

### Risk and Business Context

A small tax consultancy may handle sensitive client tax, financial, and identity-related information. Weak endpoint visibility could allow unauthorized execution, persistence, discovery, or concealment activity to remain unnoticed.

The project therefore focuses on improving visibility and detection rather than demonstrating unauthorized access to real systems.

### Limitation

The primary limitation was the inability to complete the planned isolated Windows virtual machine because of insufficient storage. As a result, the validated endpoint tests were restricted to harmless activities on the student's own Windows 10 Pro system.

The default Sysmon configuration also did not provide FileCreate Event ID 11 telemetry for the tested file-creation scenario.

### Future Improvement

A future iteration should use a properly isolated Windows virtual machine with a dedicated Sysmon configuration covering additional telemetry such as file creation, registry modification, process access, and network connections. Additional Sigma rules could then be validated against a broader range of endpoint events.

### Evidence

Detailed screenshots and validation evidence are maintained separately in the submitted report and are not included in the public GitHub repository.

### Conclusion

The project demonstrated a complete basic detection-engineering workflow: controlled activity generation, Sysmon telemetry collection, ATT&CK mapping, Sigma rule development, validation, troubleshooting, and cleanup. The three validated Sigma rules demonstrate that endpoint telemetry generated by controlled activity can be converted into repeatable detection logic for a small-business environment.
