---
title: "Descobrindo um Ataque Massivo da Cadeia de Fornecimento do GitHub: Quando o Repositório de um Amigo Morde de Volta"
date: 2026-04-29T00:00:00+00:00
draft: false
tags: ["Segurança", "GitHub", "Cadeia de Fornecimento", "Malware", "Cibersegurança"]
weight: -10
categories: ["Segurança", "Programação"]
---

Começou como qualquer outro dia. Estava casualmente a rever um repositório GitHub de um amigo quando um bloco gigantesco de texto ilegível me chamou a atenção. Estava sentado ali dentro de um ficheiro Python, mas as variáveis eram puro disparate. O meu sentido de cibersegurança começou a formiguejar imediatamente—isto era código altamente ofuscado.

O que não sabia naquele momento era que tinha acabado de descobrir um ataque massivo e altamente sofisticado da cadeia de fornecimento que infetava centenas de repositórios em todo o GitHub.

Aqui está a história de como o encontrei, o descodifiquei, e como pode proteger os seus próprios repositórios.

## O Fragmento Suspeito

O código que encontrei parecia assim. É uma técnica clássica de ofuscação: esconder a verdadeira intenção do script atrás de camadas aninhadas de codificação e execução dinâmica.

```python
# -*- coding: utf-8 -*-
aqgqzxkfjzbdnhz = __import__('base64')
wogyjaaijwqbpxe = __import__('zlib')
idzextbcjbgkdih = 134
qyrrhmmwrhaknyf = lambda dfhulxliqohxamy, osatiehltgdbqxk: bytes([wtqiceobrebqsxl ^ idzextbcjbgkdih for wtqiceobrebqsxl in dfhulxliqohxamy])
lzcdrtfxyqiplpd = 'eNq9W19z3MaRTy......SN' # Cadeia base64 massiva truncada
runzmcxgusiurqv = wogyjaaijwqbpxe.decompress(aqgqzxkfjzbdnhz.b64decode(lzcdrtfxyqiplpd))
ycqljtcxxkyiplo = qyrrhmmwrhaknyf(runzmcxgusiurqv, idzextbcjbgkdih)
exec(compile(ycqljtcxxkyiplo, '<>', 'exec'))
```

Observando as últimas três linhas, o fluxo de execução era claro:
1. Descodificar de Base64.
2. Descomprimir usando Zlib.
3. Desencriptar usando uma operação XOR (com a chave `134`).
4. Executar o carregamento malicioso diretamente na memória usando a função altamente perigosa `exec()`.

## Quebrando o Código

Inicialmente tentei desencriptar manualmente o carregamento, mas lidar com a cadeia massiva e as operações aninhadas estava a ficar tedioso. Portanto, coloquei um assistente de IA (Claude) num ambiente isolado e pedi-lhe para escrever um "desofuscador" seguro.

O objetivo era simples: substituir o perigoso `exec()` por uma declaração `print()` para despejar o carregamento oculto como texto simples sem realmente o executar.

Aqui está o script que usámos para desativar e extrair o carregamento:

```python
import base64
import zlib

key = 134
# A cadeia gigantesca vai aqui
payload_base64 = 'eNq9W19z3MaRTy...' 

# Desembrulhar as camadas
decompressed_data = zlib.decompress(base64.b64decode(payload_base64))
decoded_bytes = bytes([b ^ key for b in decompressed_data])
hidden_script = decoded_bytes.decode('utf-8')

print("--------------------------------------------------")
print("🚨 O CARREGAMENTO OCULTO É: 🚨\n")
print(hidden_script)
print("\n--------------------------------------------------")
```

## O Monstro Interior

Executar o descodificador numa caixa de areia revelou a verdadeira natureza da besta. O script Python resultante era um **Dropper/Loader** altamente sofisticado concebido para roubar informações.

*(Nota: O carregamento desencriptado completo é massivo, mas aqui estão as características principais e aterrorizantes que continha)*

1. **Comando e Controlo da Blockchain (C2):** Em vez de se conectar a um endereço IP tradicional e facilmente bloqueável, o malware consulta a **blockchain Solana**. Procura no histórico de transações de uma carteira específica e extrai comandos encriptados escondidos dentro dos "Memos" das transações. Isto torna praticamente impossível derrotar a infraestrutura do atacante.
2. **Geofencing (A Exceção Russa):** O script inclui uma função chamada `_isRussianSystem()`. Verifica a língua, fuso horário e localização do sistema. Se a máquina infetada estiver localizada na Rússia ou em países da CEI, o malware sai silenciosamente. Esta é uma tática clássica usada por atores maliciosos para evitar a atenção das autoridades policiais locais.
3. **Traga o Seu Próprio Ambiente:** O malware detecta silenciosamente o seu sistema operativo (Windows, macOS ou Linux) e descarrega uma versão portátil do **Node.js** diretamente do site oficial. Depois usa este Node.js descarregado para executar um ficheiro JavaScript secundário e invisível (provavelmente um stealer como Lumma ou RedLine) para siphon palavras-passe, cookies e carteiras de criptografia.

## A Escala da Infeção

Pensando que isto poderia ser um incidente isolado, peguei num fragmento do código ofuscado e procurei-o em todo o GitHub.

Os resultados foram arrepiantes. **Mais de 300 repositórios foram infetados com este código exato.** Após alguns investigação, parece que este código malicioso está a ser injetado diretamente em ficheiros durante o processo de `git commit`. Embora o vetor inicial exato (seja uma extensão VS Code comprometida, um pacote npm/PyPI malicioso, ou uma ferramenta de terminal sequestrada) ainda seja um mistério que estou a investigar, o resultado é claro: os programadores estão involuntariamente a colocar malware nos seus próprios repositórios.

## O Verdadeiro Perigo: Modelos de IA Envenenados

Mas aqui está o que me mantém acordado à noite: **dados de treino de IA.**

Milhares de engenheiros de aprendizagem automática e investigadores de IA estão ativamente a fazer scraping de repositórios GitHub para treinar os seus grandes modelos de linguagem (LLMs), modelos de geração de código, e ferramentas de análise de segurança. Estão a tratar código de fonte aberta como "dados de treino gratuitos." O que não percebem é que potencialmente estão a aspirar estes fragmentos ofuscados e maliciosos e a alimentá-los diretamente para as suas redes neurais.

Imagine um cenário onde um modelo de IA é treinado em 300+ repositórios infetados. O código malicioso, incorporado profundamente dentro de milhares de amostras de código legítimo, torna-se parte dos padrões aprendidos do modelo. Avançando para a produção: os programadores usam este modelo "treinado" para:
- Gerar sugestões de código (e o modelo sugere malware ofuscado)
- Analisar vulnerabilidades de segurança (mas o próprio modelo contém backdoors ocultos)
- Validar dependências de terceiros (enquanto involuntariamente recomenda pacotes comprometidos)

O cenário de pesadelo não é apenas um modelo envenenado—é um indetectável. O malware vive dentro dos pesos e vieses matemáticos da rede neural, invisível para qualquer análise estática de código. Não acionará caixas de areia ou scanners de antivírus porque não é código "em execução"; está incorporado como comportamento aprendido. Nunca o encontraria até o modelo começar a gerar sugestões maliciosas em produção, potencialmente comprometendo milhares de projetos posteriores simultaneamente.

Este é um ataque da cadeia de fornecimento que transcende repositórios e infeta as próprias ferramentas que usamos para escrever código seguro.

## A Mitigação: Uma Solução Rápida

Até identificarmos exatamente qual ferramenta ou pacote está a sequestrar o processo de commit, precisamos de uma forma de parar o sangramento.

Como este malware depende de injetar uma cadeia Base64 massiva e contínua no seu código, a forma mais fácil de evitar que o seu repositório seja infetado (e o propague a outros) é configurar um **Hook Pré-commit** rigoroso.

Pode bloquear qualquer commit que contenha uma cadeia anormalmente longa (por exemplo, mais de 100 caracteres sem espaços). Aqui está um conceito simples para um hook pré-commit do Git que pode adicionar ao ficheiro `.git/hooks/pre-commit`:

```bash
#!/bin/bash
# Um simples hook pré-commit para apanhar injeções massivas de base64

if git diff --cached | grep -E '[a-zA-Z0-9+/]{100,}'; then
    echo "🚨 ALERTA DE SEGURANÇA: Uma cadeia suspeita longa (possível carregamento Base64) foi detetada."
    echo "Commit rejeitado. Por favor, reveja o seu código para malware injetado."
    exit 1
fi
```

## Conclusão

Os ataques da cadeia de fornecimento estão a ficar mais inteligentes. Já não estão apenas a visar servidores de produção; estão a viver dentro dos nossos ambientes de desenvolvimento, sequestram os nossos commits, e usam blockchains descentralizadas para esconder os seus rastos.

Verifique os seus repositórios, reveja as suas dependências, e se vir um bloco gigantesco de letras aleatórias nos seus ficheiros Python, não o execute. Mantenha-se seguro!
