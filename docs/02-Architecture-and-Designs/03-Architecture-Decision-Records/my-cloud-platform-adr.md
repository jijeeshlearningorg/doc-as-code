# 02. Architecture Decision Record (ADR) template

* Status: Draft, Proposed, Accepted, Rejected  
* Owner: An ADR owner  
* Deciders: People involved in decision-making  
* Working group: Owners, the group of people or single person responsible for implementing the requirement  
* Creation Date: 2024-02-08  
* Last Revisited: 2024-02-08  
* Revision: A version number  

## Context and Problem Statement

## Generated Context Summary

| Field | Value |
|----------|----------|
| Product | My Cloud Services |
| Source Repository | `jijeeshlearningorg/brownfield-code` |
| Generated Date | 2026-07-31 |

### Impacted Capabilities

- capacity-management
- lifecycle-management
- migration
- observability

### Changed Files

- src/capacity_calc.py
- src/migrate.py
- src/patch.py
- src/rollback.py
- src/upgrade.py
- src/validation.py

### Detected Functions

- apply_security_patch
- calculate_energy_savings
- check_patch_compliance
- create_restore_point
- estimate_capacity_growth
- evacuate_virtual_machines
- execute_rollback
- generate_capacity_recommendation
- migrate_legacy_hardware_node
- validate_migration_prerequisites
- validate_patch_success
- verify_rollback_status

Describe the architectural design issue you’re addressing, leaving no questions about why you’re addressing this issue now. Following a minimalist approach, address and document only the issues that need addressing at various points in the life cycle.

## Assumptions (Optional)

Clearly describe the underlying assumptions in the environment in which you’re making the decision—cost, schedule, technology, and so on. Note that environmental constraints (such as accepted technology standards, enterprise architecture, commonly employed patterns, and so on) might limit the alternatives you consider.

## Constraints (Optional)

Capture any additional environmental constraints that the chosen alternative (the decision) might pose.

## Considered Options

Document alternatives, concerns, ancillary or related issues, and questions that arose in the debate of the ADR.

## Proposed Design (Optional)

Document the design.

## Decision Outcome

Clearly state the architecture’s direction—that is, the position you’ve selected.

## Related Artifacts (Optional)

List the related architecture, design, other ADRs, or scope documents that this decision impacts.

## Comments (Optional)

Put all comments from people engaged in the ADR process. You may create a JIRA story and ask people to put their comments or simply create a PR.
