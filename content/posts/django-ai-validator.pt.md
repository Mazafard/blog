---
title: "Apresentando o Django AI Validator: Quando Regex Não Basta"
date: 2025-12-01T00:00:00+00:00
draft: false
tags: ["Django", "IA", "Validação", "LLM"]
weight: -9
categories: ["Programação"]
---

Regex resolve e-mails, CEPs e outras strings bonitinhas. Mas desmonta na hora em que alguém pede: "garanta que essa bio soe profissional" ou "confira se o anúncio realmente descreve um carro". Dá para empilhar mais padrões, só que você continua julgando sintaxe, não significado.

**Django AI Validator** preenche esse vazio semântico. É um pacote novinho no PyPI que conecta seus campos Django a LLMs modernos (OpenAI, Anthropic, Gemini ou até um Ollama local) para validar e limpar o texto durante o fluxo normal do formulário.

## Por Que Mais Um Validador?

Porque qualidade de conteúdo já não se resume a caracteres permitidos. Precisamos detectar tom, intenção e violações de política sem construir uma esteira de moderação do zero. Em vez de brincar de caça-palavras, você descreve em linguagem natural o que é "bom" e o validador faz o resto.

## O Que o Pacote Entrega

- **Validação semântica:** `AISemanticValidator` lê seu prompt ("Esse texto é respeitoso e relevante?") e avalia o conteúdo nessa linha.
- **Limpeza automática:** `AICleanedField` reescreve o input antes de persistir, remove PII, ajusta gramática e normaliza gírias.
- **Mimos no Django Admin:** Dados suspeitos aparecem com indicadores visuais e ações em massa para limpeza.
- **Suporte a async:** Chamadas demoradas ao LLM podem ir para tasks Celery, mantendo o request-response ágil.

Documentação: [mazafard.github.io/Django-AI-Validator](https://mazafard.github.io/Django-AI-Validator/)
Código aberto: [github.com/Mazafard/Django-AI-Validator](https://github.com/Mazafard/Django-AI-Validator)

## Tour de Arquitetura (a.k.a. o Cantinho Nerd)

Para ficar agnóstico de provedor e pronto para produção, o núcleo usa padrões clássicos de design:

1. **Adapter Pattern:** `LLMAdapter` define `validate`/`clean`. Implementações como `OpenAIAdapter`, `AnthropicAdapter`, `GeminiAdapter` e `OllamaAdapter` traduzem tudo para cada API.
2. **Abstract Factory:** `AIProviderFactory` entrega o bundle certo (adapter + config) quando você pede "openai" ou "ollama", sem `if`s espalhados.
3. **Singleton Cache:** `LLMCacheManager` mantém um cache compartilhado de prompt+payload para evitar custos duplicados ao repetir a mesma validação.
4. **Proxy Pattern:** `CachingLLMProxy` envolve qualquer adapter, verifica o cache primeiro e só chama o real em caso de miss, salvando o resultado automaticamente.
5. **Facade Pattern:** `AICleaningFacade` esconde toda a tubulação; os validators apenas chamam `facade.validate()` ou `facade.clean()`.
6. **Template Method:** `AISemanticValidator` descreve o fluxo `prepare_input → call_llm → parse_response → raise_validation_error`. Quem precisa pode sobrescrever etapas sem quebrar o algoritmo.

## Usando em um Model

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

Nos bastidores, a facade escolhe o adapter adequado, o proxy passa pelo cache e o validator ou levanta `ValidationError` ou retorna o texto limpo.

## Guia Rápido

1. `pip install django-ai-validator`
2. Configure as credenciais OpenAI/Anthropic/Gemini/Ollama no `settings`
3. Adicione `AISemanticValidator` ou `AICleanedField` nos campos importantes

Pronto. Em vez de escrever 100 linhas de regex para adivinhar se um review é respeitoso, você descreve o comportamento esperado e deixa a decisão para o LLM. Me conta depois o que você resolveu validar!
