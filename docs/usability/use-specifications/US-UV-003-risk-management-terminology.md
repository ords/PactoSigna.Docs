# Risk Management - Terminology

## User-Facing Labels

| Label                                  | Definition                                                                             |
| -------------------------------------- | -------------------------------------------------------------------------------------- |
| **Risk Matrix**                        | Page title. A visual representation of risks plotted on severity vs. probability axes. |
| **Inherent Risk (Before Mitigations)** | Risk levels assessed without considering any mitigation measures.                      |
| **Residual Risk (After Mitigations)**  | Risk levels assessed after mitigation measures are applied.                            |
| **Heatmap**                            | A color-coded grid displaying risk counts at each severity/probability intersection.   |

## Risk Type Filters

| Button Label  | Internal Value   | Description                    |
| ------------- | ---------------- | ------------------------------ |
| **All**       | (empty)          | Shows all risk types combined. |
| **Software**  | `software_risk`  | Software-related risks.        |
| **Usability** | `usability_risk` | Usability-related risks.       |
| **Security**  | `security_risk`  | Security-related risks.        |

## Summary Chips

| Label                    | Chip Color       | Variant  | Definition                                                               |
| ------------------------ | ---------------- | -------- | ------------------------------------------------------------------------ |
| **Total: {N}**           | Default          | Filled   | Total count of all assessed risks.                                       |
| **Acceptable: {N}**      | Success (green)  | Outlined | Risks within acceptable tolerance levels.                                |
| **Review Required: {N}** | Warning (orange) | Outlined | Risks that need additional assessment or mitigation review.              |
| **Unacceptable: {N}**    | Error (red)      | Outlined | Risks exceeding acceptable levels that must be addressed before release. |

## Heatmap Legend

| Color            | Meaning                                                |
| ---------------- | ------------------------------------------------------ |
| Green (#4caf50)  | Acceptable -- Risk is within tolerance.                |
| Orange (#ff9800) | Review Required -- Risk needs additional assessment.   |
| Red (#f44336)    | Unacceptable -- Risk must be mitigated before release. |

## Related Document Types

Risk data is sourced from these Document types:

| Document Type           | Description                                                                                  |
| ----------------------- | -------------------------------------------------------------------------------------------- |
| **Software Risk**       | Risk assessment for software-related hazards.                                                |
| **Usability Risk**      | Risk assessment for usability-related hazards.                                               |
| **Security Risk**       | Risk assessment for security-related hazards.                                                |
| **Software SoE**        | Software Sequence of Events (hazard analysis).                                               |
| **Usability SoE**       | Usability Sequence of Events (hazard analysis).                                              |
| **Security SoE**        | Security Sequence of Events (hazard analysis).                                               |
| **Hazardous Situation** | A circumstance in which people, property, or the environment are exposed to hazards.         |
| **Harm**                | Physical injury or damage to health, including damage occurring through loss of IT security. |

## Risk Gaps

| Label                   | Definition                                                                  |
| ----------------------- | --------------------------------------------------------------------------- |
| **Risk Gaps Accordion** | An expandable section showing Documents with incomplete risk documentation. |
