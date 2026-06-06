# Glossary

## MVP

Core-value tracer bullet, not full secure shell. User can type one already-paid THB expense, app creates editable local draft, user can confirm intent; no Firefly write required yet.

## Core-value tracer bullet

Smallest end-to-end slice proving product value before auth, OpenAPI, Firefly cache, or ledger sync hardening.

## Confirmed draft

A locally accepted draft proving user intent. It does not require Firefly account/category selection and does not write any Firefly transaction. It can show a later-sync placeholder.

## Draft correction path

Future enhancement where multi-turn chat patches an existing draft; not part of current MVP tracer bullet.

## Local MVP draft store

Browser-owned draft storage for MVP, typically local state/localStorage. It avoids backend persistence/auth/security until the core capture → draft → confirm interaction is proven.

## MVP parser

LLM-backed one-shot parse into an editable draft. Manual field editing handles wrong AI output in MVP.
