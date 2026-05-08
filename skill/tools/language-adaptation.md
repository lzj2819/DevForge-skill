# Language Adaptation

## Purpose

Standardize language handling across all DevForge skills.

## Rule

- System instructions and constraints in this skill are in **English** for maximum model compliance
- User-facing gate messages, summaries, and explanations use the **same language as the user's most recent input**
- If the user writes in Chinese, respond in Chinese. If English, respond in English

## Rationale

English system instructions produce more reliable structured outputs (XML, JSON, code) because the model's training data for these formats is predominantly English. User-facing content adapts to the user's language to maximize comprehension and comfort.

## Usage

This rule is automatically injected into all skills via `references/system-prompt-template.md`. Individual SKILL.md files do not need to repeat this section.
