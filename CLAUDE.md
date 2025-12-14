# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This repository contains reusable GitHub Actions workflows meant to be called from other repositories using `workflow_call`.

## Architecture

Workflows are stored in `.github/workflows/` and follow the reusable workflow pattern:
- Triggered via `workflow_call` (not `push`/`pull_request`)
- Accept inputs and secrets from calling workflows
- Can expose outputs back to callers

## Code Style

- 2 spaces for indentation (YAML files)
- UTF-8 encoding
- LF line endings
- Trim trailing whitespace
- Insert final newline
