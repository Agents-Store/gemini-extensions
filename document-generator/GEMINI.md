# document-generator

> Professional document generator. Creates proposals, invoices, estimates/quotations, reports, presentations, contracts, NDAs, and certificates of completion in PDF, DOCX, and PPTX formats. Supports multi-language documents with embedded fonts (Cyrillic, Latin). First-use onboarding for style preferences, company profiles, and logo management. Converts between MD, DOCX, PDF, HTML, and PPTX.

Canonical source: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/document-generator

## Skills

- **design-best-practices** — Comprehensive library of professional document design best practices sourced from top consulting firms (McKinsey, Deloitte, BCG), tech leaders (Stripe, Apple, Google), and typography/layout research. Use this skill when making design decisions, choosing fonts, colors, layouts, or when the user asks for a "professional" or "corporate" look.
- **document-generator** — Document generation process -- format selection, data collection, script invocation, and delivery. This skill should be used when generating any document (proposal, invoice, estimate, report, presentation, contract, NDA, certificate of completion), deciding which format or engine to use, or running generation scripts.
- **document-templates** — Document template structures and data collection checklists for each document type (proposal, invoice, estimate, report, presentation, contract, NDA, certificate of completion). This skill should be used when determining what data to collect from the user, what fields are required or optional, or how to structure a specific document type.
- **examples** — End-to-end document generation examples, workflow walkthroughs, and complete JSON input samples for all document types (proposal, invoice, estimate, report, presentation, contract, NDA, certificate of completion). This skill should be used when the user asks for a worked example, wants to see a sample document, needs a template pattern to follow, or asks how to create a specific document step by step.
- **formatting-standards** — Typography, font, margin, color, and layout standards for professional business documents. Use this skill when making any formatting decisions — choosing fonts, colors, margins, spacing, or layout patterns for any document type.
- **user-preferences** — First-use onboarding, user style preferences, company profiles, and logo management for document generation. This skill should be used on the first document generation request to collect user preferences, when the user wants to change their style, or when managing company logos and branding profiles.

## Commands

- `/convert-document` — Convert between document formats (MD/DOCX/PDF/HTML/PPTX)
- `/generate-act` — Generate an Act of Completed Works (PDF)
- `/generate-contract` — Generate a contract or agreement (DOCX/PDF)
- `/generate-estimate` — Generate a professional cost estimate / quotation (PDF)
- `/generate-invoice` — Generate a professional invoice (PDF)
- `/generate-nda` — Generate a Non-Disclosure Agreement (NDA) — PDF
- `/generate-presentation` — Generate a professional presentation (PPTX)
- `/generate-proposal` — Generate a professional business proposal (DOCX/PDF)
- `/generate-report` — Generate a professional report (DOCX/PDF)
- `/setup` — Set up document generator preferences — style, language, company profile, and logo.
