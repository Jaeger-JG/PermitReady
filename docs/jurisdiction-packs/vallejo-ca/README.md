# City of Vallejo — ADU Building Permit Pack

Pilot jurisdiction configuration. **Verify all requirements against official city sources before production use.**

- [Vallejo Building Division](https://www.cityofvallejo.net/our_city/departments_divisions/planning_development_services/building_division)

## Configuration (YAML)

```yaml
jurisdiction: vallejo-ca
permitType: adu-building-permit
name: ADU Building Permit
externalApplyUrl: https://www.cityofvallejo.net/our_city/departments_divisions/planning_development_services/building_division
codePack: cbc_2022_ca_amended

requiredDocuments:
  - type: architectural_plans
    format: pdf
    required: true
    label: Architectural Plans
  - type: structural_calculations
    format: pdf
    required: true
    label: Structural Calculations
  - type: title24_energy
    format: pdf
    required: true
    label: Title 24 Energy Report (CF1R)
  - type: site_plan
    format: pdf
    required: true
    label: Site Plan

requiredSheets:
  - { id: cover_sheet, name: "Cover Sheet / Sheet Index", discipline: architectural }
  - { id: site_plan, name: "Site Plan", discipline: architectural }
  - { id: floor_plan, name: "Floor Plan", discipline: architectural }
  - { id: elevations, name: "Exterior Elevations", discipline: architectural }
  - { id: sections, name: "Building Sections", discipline: architectural }
  - { id: structural_plan, name: "Structural Plans", discipline: structural }

checklist:
  - { id: designer_stamp, description: "Architect or designer stamp on cover sheet", severity: critical }
  - { id: owner_signature, description: "Owner signature on application", severity: warning }
  - { id: val_job_number, description: "VAL job number on sheets (if applicable)", severity: info }

rules:
  - { id: adu_max_sqft, type: numeric, code: "Gov. Code 65852.2", max: 1200, unit: sqft, phase: 2 }
  - { id: adu_side_setback, type: numeric, code: "Vallejo MC + CRC", min: 4, unit: feet, phase: 2 }
```
