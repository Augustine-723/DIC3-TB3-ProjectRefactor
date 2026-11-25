# Spec Delta

## Purpose

Defines the requirements for packaging the Overleaf project archive, updating repository documentation, maintaining commit discipline, and safeguarding robot control code.

## ADDED Requirements

### Requirement: Overleaf Upload Archive Generation
A self-contained archive ICEIB/overleaf-upload.zip SHALL be generated containing all LaTeX source files, style files, bibliography databases, and figures necessary for immediate compilation on Overleaf.

#### Scenario: ZIP archive completeness
- **WHEN** extracting ICEIB/overleaf-upload.zip
- **THEN** all files required to compile main.tex without external assets SHALL be present.

### Requirement: Overleaf Integration Status Transparency
The system SHALL accurately report whether an Overleaf project was programmatically created or provide step-by-step manual import instructions when Overleaf MCP is unauthenticated.

#### Scenario: Unauthenticated Overleaf status handling
- **WHEN** Overleaf MCP credentials are not configured
- **THEN** the system SHALL NOT claim project creation and SHALL provide clear instructions for importing overleaf-upload.zip.

### Requirement: Repository Documentation Update
The root README.md SHALL be updated to document the DIC4 IEEE conference draft workflow, directory layout, artifact paths, and Overleaf import instructions while preserving all DIC3 sections.

#### Scenario: README reflects DIC4 additions
- **WHEN** reading README.md
- **THEN** the document SHALL contain dedicated sections for the ICEIB conference draft and Overleaf deliverable.

### Requirement: Preservation of Robot Control Code
The implementation workflow SHALL NOT modify any ROS 2 robot control code, Nav2 packages, or physical launch configurations, and SHALL NOT execute physical robot movement commands.

#### Scenario: Robot code remains untouched
- **WHEN** checking git status across the workspace
- **THEN** no ROS 2 nodes, launch files, or hardware control scripts SHALL be modified.
