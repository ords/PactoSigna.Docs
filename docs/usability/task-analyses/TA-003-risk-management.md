# Risk Management - User Journey

## Overview

Risk Management provides a visual risk matrix (heatmap) for medical device software, showing both inherent risk (before mitigations) and residual risk (after mitigations). It also surfaces gaps in risk documentation. Risk data is derived from risk-type Documents synced from the repository.

---

## Flow 1: View Risk Matrix

### Entry Point

Member navigates to the Risk Matrix page from a device repository context.

### Steps

1. **View the page header** -- The system displays "Risk Matrix" as the page title.

2. **Filter by risk type** -- A toggle button group allows the Member to filter:
   - **All** -- Shows all risk types combined.
   - **Software** -- Filters to software risks only.
   - **Usability** -- Filters to usability risks only.
   - **Security** -- Filters to security risks only.
     The selected filter is persisted in the URL query parameter (`?type=software_risk`).

3. **Review the summary** -- A summary card displays chips showing:
   - **Total**: Total number of risks.
   - **Acceptable** (green, outlined): Risks within acceptable levels.
   - **Review Required** (orange, outlined): Risks that need further assessment.
   - **Unacceptable** (red, outlined): Risks that exceed acceptable levels.

4. **Analyze heatmaps** -- Two heatmaps are displayed side by side:
   - **Inherent Risk (Before Mitigations)** -- Shows the risk distribution before any mitigation measures.
   - **Residual Risk (After Mitigations)** -- Shows the risk distribution after mitigation measures are applied.
     Each heatmap is a grid where the axes represent severity and probability. Cells are colored based on acceptability levels.

5. **Read the legend** -- A legend card below the heatmaps explains the color coding:
   - Green: Acceptable
   - Orange: Review Required
   - Red: Unacceptable

6. **Review risk gaps** -- Below the heatmaps, a Risk Gaps Accordion shows:
   - Documents with incomplete risk assessments.
   - Missing fields or relationships.
     This section loads alongside the matrix data.

### Empty / Error States

- **No organization** -- "Please select an organization" alert.
- **Loading** -- A centered spinner is shown while data loads.
- **Error** -- "Failed to load risk data" error alert.
- **No data** -- "No risk data available" info alert.
