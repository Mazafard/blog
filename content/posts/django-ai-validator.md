---
title: "Introducing Django AI Validator: When Regex Isn't Enough"
date: 2025-12-01T00:00:00+00:00
draft: false
tags: ["Django", "AI", "Validation", "LLM"]
weight: -9
categories: ["Programming"]
---

Regex is perfect for emails, postal codes, and other tidy little strings. It completely falls apart when the requirement sounds like, "make sure this bio feels professional" or "double-check that the listing actually describes a car." You can throw more patterns at it, but you're still judging syntax, not meaning.

**Django AI Validator** is my answer for the semantic gap. It's a fresh PyPI package that lets your Django fields tap into modern LLMs (OpenAI, Anthropic, Gemini, or even local Ollama) for on-the-fly validation and cleaning.

## Why Another Validator?

Because content quality is no longer about allowed characters. We need to spot tone, intent, and policy violations without building a bespoke moderation pipeline. Instead of juggling keyword filters and brittle heuristics, you describe what “good” looks like in plain English (or any language), and the validator handles the rest.

## What the Package Delivers

- **Semantic Validation:** `AISemanticValidator` reads your prompt (“Is this description respectful and on-topic?”) and evaluates the text accordingly.
- **Automatic Cleaning:** `AICleanedField` can rewrite input -- remove PII, fix grammar, normalize slang -- before it ever hits your database.
- **Admin UX perks:** Dirty data gets flagged inside Django admin with bulk clean actions so your moderators stay in the loop.
- **Async friendly:** Long-running LLM calls can hop to Celery tasks so your main request stays snappy.

Docs live at [mazafard.github.io/Django-AI-Validator](https://mazafard.github.io/Django-AI-Validator/) and the code is open-source at [github.com/Mazafard/Django-AI-Validator](https://github.com/Mazafard/Django-AI-Validator).

## Architecture Tour (a.k.a. the Nerd Corner)

I wanted this library to stay provider-agnostic and production-ready, so the internals lean on classic design patterns:

1. **Adapter Pattern:** `LLMAdapter` defines a common `validate`/`clean` contract. Concrete adapters (`OpenAIAdapter`, `AnthropicAdapter`, `GeminiAdapter`, `OllamaAdapter`) translate calls to each vendor without leaking API quirks into your models.
2. **Abstract Factory:** `AIProviderFactory` hands you the correct adapter + config bundle when you ask for "openai" or "ollama". No scattered `if provider == ...` statements.
3. **Singleton Cache:** `LLMCacheManager` keeps a shared cache of prompt+payload combos so repeat validations hit memory instead of your billable token quota.
4. **Proxy Pattern:** `CachingLLMProxy` wraps any adapter, checks the cache first, then forwards on a miss and stores the response. No code changes needed on your end.
5. **Facade Pattern:** `AICleaningFacade` hides all of the above. Validators just call `facade.validate()` or `facade.clean()` without caring about factories, proxies, or retries.
6. **Template Method:** `AISemanticValidator` formalizes the flow: `prepare_input → call_llm → parse_response → raise_validation_error`. Override the pieces you need; keep the skeleton intact.

## Using It In a Model

```python
from django.db import models
from django_ai_validator.validators import AISemanticValidator
from django_ai_validator.fields import AICleanedField

class Product(models.Model):
    name = models.CharField(
        max_length=100,
        validators=[
            AISemanticValidator(
                prompt_template="Check if this name is catchy and marketing-friendly. Return VALID if yes."
            )
        ],
    )

    description = AICleanedField(
        cleaning_prompt="Fix grammar, remove profanity, and keep it professional.",
        use_async=True,
    )
```

Behind the scenes, the facade picks the right adapter, the proxy checks the cache, and the validator either raises `ValidationError` or quietly returns the cleaned payload.

## Getting Started

1. `pip install django-ai-validator`
2. Drop your OpenAI/Anthropic/Gemini/Ollama creds into settings
3. Add `AISemanticValidator` or `AICleanedField` to the fields you care about

That's it. Instead of writing 100 lines of regex to guess whether a review is respectful, you describe the behavior you expect and let an LLM do the judgment call. Give it a spin and tell me what you end up validating. I'm genuinely curious.
