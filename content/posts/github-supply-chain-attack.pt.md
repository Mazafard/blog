---
title: "A Descoberta de um Mega Ataque à Cadeia de Fornecimento no GitHub: Quando o Repo de um Amigo Ataca!"
date: 2026-04-29T00:00:00+00:00
draft: false
tags: ["Security", "GitHub", "Supply Chain", "Malware", "Cybersecurity"]
weight: -10
categories: ["Security", "Programming"]
---

Tudo começou como um dia qualquer. Estava a dar uma vista de olhos no repositório do GitHub de um amigo quando um bloco de texto gigante e ilegível me chamou a atenção. Estava ali sossegado dentro de um ficheiro Python, mas as variáveis não faziam sentido nenhum. O meu sentido aranha de cibersegurança disparou logo — isto era código altamente ofuscado.

O que eu não sabia naquele momento era que tinha acabado de tropeçar num ataque massivo e super sofisticado à cadeia de fornecimento (*supply chain*), a infetar centenas de repositórios por todo o GitHub.

Aqui fica a história de como o encontrei, de como fiz engenharia reversa e como podes proteger os teus próprios projetos.

## O Snippet Suspeito

O código que encontrei tinha este aspeto. É uma técnica clássica de ofuscação: esconder a verdadeira intenção do script atrás de camadas aninhadas de codificação e execução dinâmica.

```python
# -*- coding: utf-8 -*-
aqgqzxkfjzbdnhz = __import__('base64')
wogyjaaijwqbpxe = __import__('zlib')
idzextbcjbgkdih = 134
qyrrhmmwrhaknyf = lambda dfhulxliqohxamy, osatiehltgdbqxk: bytes([wtqiceobrebqsxl ^ idzextbcjbgkdih for wtqiceobrebqsxl in dfhulxliqohxamy])
lzcdrtfxyqiplpd = 'eNq9W19z3MaRTy......SN' # Massive base64 string truncated
runzmcxgusiurqv = wogyjaaijwqbpxe.decompress(aqgqzxkfjzbdnhz.b64decode(lzcdrtfxyqiplpd))
ycqljtcxxkyiplo = qyrrhmmwrhaknyf(runzmcxgusiurqv, idzextbcjbgkdih)
exec(compile(ycqljtcxxkyiplo, '<>', 'exec'))
```

A olhar para as últimas três linhas, o fluxo de execução era claro: 
1. Descodificar de Base64.
2. Descomprimir usando Zlib.
3. Desencriptar usando uma operação XOR (com a chave `134`).
4. Executar o payload malicioso diretamente na memória usando a função altamente perigosa `exec()`.

## A Quebrar o Código 

Inicialmente tentei desencriptar o payload à mão, mas lidar com a string gigante e as operações aninhadas estava a dar cabo da minha paciência. Por isso, abri um assistente de IA (Claude) num ambiente isolado e pedi-lhe para escrever um "desofuscador" seguro. 

O objetivo era simples: substituir o perigoso `exec()` por um `print()` para despejar o payload escondido em texto limpo, sem o executar de verdade.

Aqui está o script que usámos para desarmar e extrair o payload:

```python
import base64
import zlib

key = 134
# The giant string goes here
payload_base64 = 'eNq9W19z3MaRTy...' 

# Unwrap the layers
decompressed_data = zlib.decompress(base64.b64decode(payload_base64))
decoded_bytes = bytes([b ^ key for b in decompressed_data])
hidden_script = decoded_bytes.decode('utf-8')

print("--------------------------------------------------")
print("🚨 THE HIDDEN PAYLOAD IS: 🚨\n")
print(hidden_script)
print("\n--------------------------------------------------")
```

## O Monstro Lá Dentro

Correr o descodificador numa *sandbox* revelou a verdadeira natureza do bicho. O script Python resultante era um **Dropper/Loader** super sofisticado, desenhado para roubar informações.

*(Nota: O payload desencriptado completo é gigante, mas aqui estão as principais características assustadoras que ele continha)*

1. **Comando e Controlo (C2) na Blockchain:** Em vez de se ligar a um endereço IP tradicional, fácil de bloquear, o malware consulta a **blockchain da Solana**. Ele procura o histórico de transações de uma carteira específica e extrai comandos encriptados escondidos dentro dos "Memos" das transações. Isto torna a desativação da infraestrutura do atacante quase impossível.
2. **Geofencing (A Exceção Russa):** O script inclui uma função chamada `_isRussianSystem()`. Ela verifica o idioma, o fuso horário e a localização do sistema. Se a máquina infetada estiver na Rússia ou em países da CEI, o malware fecha-se silenciosamente. Esta é uma tática clássica usada por hackers para evitar chamar a atenção das autoridades locais.
3. **Traz o Teu Próprio Ambiente (BYOE):** O malware deteta silenciosamente o teu sistema operativo (Windows, macOS ou Linux) e descarrega uma versão portátil do **Node.js** diretamente do site oficial. Depois, usa esse Node.js para executar um segundo ficheiro JavaScript invisível (provavelmente um *stealer* como o Lumma ou RedLine) para roubar passwords, cookies e carteiras de criptomoedas.

## A Escala da Infeção

A pensar que isto poderia ser um caso isolado, peguei num bocado do código ofuscado e pesquisei-o pelo GitHub inteiro. 

Os resultados deram-me arrepios. **Mais de 300 repositórios estavam infetados com exatamente este mesmo código.** Após alguma investigação, parece que este código malicioso está a ser injetado diretamente nos ficheiros durante o processo de `git commit`. Embora o vetor inicial exato (seja uma extensão do VS Code comprometida, um pacote npm/PyPI malicioso ou uma ferramenta de terminal pirateada) ainda seja um mistério que estou a investigar, o resultado é claro: os *developers* estão a fazer *push* de malware para os seus próprios repositórios sem saberem.

## O Verdadeiro Perigo: Modelos de IA Envenenados

Mas aqui está o que me tira o sono: **dados de treino de IA.**

Milhares de engenheiros de *machine learning* e investigadores de IA andam ativamente a fazer *scraping* de repositórios do GitHub para treinar os seus grandes modelos de linguagem (LLMs), modelos de geração de código e ferramentas de análise de segurança. Estão a tratar o código open-source como "dados de treino gratuitos". O que não percebem é que podem estar a aspirar estes pedaços de código malicioso e ofuscado, alimentando-os diretamente às suas redes neuronais.

Imagina um cenário em que um modelo de IA é treinado em mais de 300 repositórios infetados. O código do malware, embutido bem fundo em milhares de exemplos de código legítimos, torna-se parte dos padrões aprendidos pelo modelo. Avançamos para a produção: os *developers* usam este modelo "treinado" para:
- Gerar sugestões de código (e o modelo sugere malware ofuscado)
- Analisar vulnerabilidades de segurança (mas o próprio modelo contém *backdoors* escondidos)
- Validar dependências de terceiros (recomendando pacotes comprometidos sem saber)

O cenário de pesadelo não é apenas um modelo envenenado — é um modelo indetetável. O malware vive dentro dos pesos e vieses matemáticos da rede neuronal, invisível para qualquer análise estática de código. Não vai acionar *sandboxes* ou antivírus porque não é código "em execução"; está embutido como um comportamento aprendido. Só davas por ela quando o modelo começasse a gerar sugestões maliciosas em produção, podendo comprometer milhares de projetos a jusante ao mesmo tempo.

Este é um ataque à cadeia de fornecimento que ultrapassa os repositórios e infeta as próprias ferramentas que usamos para escrever código seguro.

## A Mitigação: Um Penso Rápido

Até descobrirmos exatamente que ferramenta ou pacote está a sequestrar o processo de commit, precisamos de uma forma de estancar a hemorragia. 

Como este malware depende da injeção de uma string Base64 gigante e contínua no teu código, a maneira mais fácil de evitar que o teu repo seja infetado (e espalhe para outros) é configurar um **Pre-commit Hook** rigoroso.

Podes bloquear qualquer commit que contenha uma string invulgarmente longa (por exemplo, mais de 100 caracteres sem espaços). Aqui está um conceito simples para um *pre-commit hook* do Git que podes adicionar ao teu ficheiro `.git/hooks/pre-commit`:

```bash
#!/bin/bash
# A simple pre-commit hook to catch massive base64 injections

if git diff --cached | grep -E '[a-zA-Z0-9+/]{100,}'; then
    echo "🚨 SECURITY ALERT: A suspiciously long string (potential Base64 payload) was detected."
    echo "Commit rejected. Please review your code for injected malware."
    exit 1
fi
```

## Conclusão

Os ataques à cadeia de fornecimento estão cada vez mais espertos. Já não atacam apenas servidores de produção; vivem dentro dos nossos ambientes de desenvolvimento, sequestram os nossos commits e usam blockchains descentralizadas para esconder o rasto. 

Verifica os teus repositórios, revê as tuas dependências e se vires um bloco gigante de letras aleatórias nos teus ficheiros Python, não o executes. Mantenham-se seguros por aí!