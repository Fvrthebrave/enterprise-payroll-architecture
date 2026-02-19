# Enterprise Payroll Processing System (Case Study)

## Overview

This repository documents the architecture and technical design of a production payroll processing system I maintained and enhanced over several years as a backend-focused software developer.

The platform processed payroll for multiple clients, integrating modern Node.js APIs with a legacy IBM i (AS/400) + DB2 backend. The system handled complex financial logic including tax calculations, wage base caps, tip credits, concurrency control, and third-party payment integrations.

Note: This repository does not contain proprietary source code. All examples are sanitized and simplified for demonstration purposes.

---

## Tech Stack

- Backend: Node.js, Express
- Language: JavaScript
- Database: DB2 (IBM i / AS400), Postgres (supporting services)
- Architecture: Layered API + Business Logic + Data Access
- Integrations: SOAP services (paycard registration), REST APIs
- Environment: Enterprise financial/payroll production system

---

## High-Level Architecture

Client UI / Admin Panel  
↓  
Node.js / Express API Layer  
↓  
Business Logic Layer  
↓  
DB2 (IBM i) Data Layer  
↓  
External Services (Tax APIs, Paycard SOAP Integration)

Key Architectural Concepts:

- Separation of API routing and financial business logic
- Idempotent payroll processing
- Concurrency locking to prevent duplicate payroll runs
- Defensive validation before tax calculation
- Controlled rounding for financial accuracy

---

## Core Responsibilities & Systems Owned

### 1. Payroll Processing Logic

- Built and refined payroll calculation flows
- Implemented wage base cap logic (FUTA / SUTA)
- Managed YTD (Year-To-Date) tracking and recalculation
- Handled edge cases for negative net pay scenarios

Example (Sanitized Wage Base Logic):

function calculateTaxableWages(ytdWages, currentWages, wageBase) {
  if (ytdWages >= wageBase) {
    return 0;
  }

  return Math.min(
    wageBase - ytdWages,
    currentWages
  );
}

---

### 2. Concurrency & Duplicate Payroll Prevention

Payroll systems must prevent double-processing.

Implemented:

- Locking endpoints to prevent simultaneous payroll runs
- Heartbeat mechanisms to track active sessions
- Defensive checks to prevent duplicate submissions
- Logging and monitoring for long-running tasks

Key principle: Financial operations must be idempotent.

---

### 3. Tip Credit & Wage Validation

Developed logic to:

- Compare minimum wage vs tipped wage
- Apply employer tip credits within legal caps
- Ensure compliance with state-specific rules
- Recalculate credits during payroll edits

---

### 4. Negative Net Pay Handling

Edge cases where deductions exceeded gross pay required:

- Deferred deduction logic
- Carry-forward mechanisms
- GL account adjustments
- Data integrity checks before finalizing payroll

---

### 5. External Integrations

- SOAP-based paycard registration
- API calls to build tax data before payroll finalization
- Data synchronization between legacy and modern services

---

## Performance & Optimization

- Reduced duplicate API calls through refactoring
- Improved bulk payroll processing performance
- Added defensive validation layers to reduce DB write errors
- Optimized YTD retrieval to prevent repeated heavy queries

---

## Real-World Engineering Challenges Solved

- Prevented duplicate payroll runs using database-level locking mechanisms.
- Implemented negative net pay correction logic to carry forward unpaid balances.
- Enforced tax wage-base caps (FUTA/SUTA) with year-to-date tracking.
- Integrated SOAP-based paycard registration APIs with robust error handling.
- Optimized DB2 queries to reduce payroll processing time across large employee sets.

## Transactional Integrity & Concurrency

Payroll runs were executed within controlled transactional boundaries to prevent:

- Duplicate processing
- Partial writes
- Corrupt year-to-date totals

Concurrency locks were implemented to ensure only one payroll process could execute per client at a time. Financial calculations were performed using controlled rounding strategies to maintain cent-level accuracy across aggregate totals.

## Lessons Learned

Working on financial systems reinforced several critical engineering principles:

- Precision matters — floating point errors must be controlled
- Financial operations must be idempotent
- Concurrency control is not optional in transactional systems
- Defensive programming prevents downstream accounting errors
- Legacy systems can be extended safely with modern API layers

---

## What This Experience Represents

This system was not a greenfield project. It was a live, production financial system serving real clients.

My role involved:

- Maintaining and improving existing architecture
- Shipping production fixes and enhancements
- Refactoring critical financial logic
- Preventing race conditions and data corruption
- Ensuring regulatory compliance logic was applied correctly

This experience strengthened my expertise in backend engineering, financial systems design, and production reliability.

---

## Future Improvements (If Rebuilding Today)

If rebuilding from scratch, I would:

- Introduce stronger typing (TypeScript across all layers)
- Implement structured event logging for payroll lifecycle stages
- Add automated financial reconciliation tests
- Separate calculation engine into a dedicated service

---

## Contact

If you'd like to discuss system design, financial backend architecture, or payroll-specific logic, feel free to connect.
