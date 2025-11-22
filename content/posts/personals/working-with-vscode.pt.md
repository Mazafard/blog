---
author: ["Maza Fard"]
date: '2025-11-22'
description: Um pouco de reclamação sobre o VS Code e uma dica para separar ficheiros não rastreados das alterações.
PTtags:
  - vscode
  - editor
  - git
  - configurações
PTcategories:
  - tecnologia
  - ferramentas
PTseries:
  - ferramentas dev
ShowToc: true
weight: 1
title: Trabalhando com o VS Code (A contragosto!)
---

## Por que é que o VS Code é tão pesado?

A sério, por que é que o VS Code é tão lento e cansado? Parece que estamos a abrir um browser inteiro só para escrever umas linhas de código. Quanto mais plugins instalamos, pior fica. Não percebo mesmo o entusiasmo.

## Por que é que toda a gente o usa?

O que me irrita ainda mais é que está a tornar-se o padrão em todo o lado. Todos os tutoriais, todas as equipas, toda a gente usa o VS Code. Sinceramente, não estou nada satisfeito com o facto de estar a dominar o mundo quando existem editores muito mais leves e rápidos. Mas pronto, não podemos lutar contra a maré.

## Já que temos de o usar...

Já que somos obrigados a usá-lo de vez em quando, mais vale aprendermos a trabalhar com ele. Hoje, vamos falar sobre como separar as alterações rastreadas dos ficheiros não rastreados (untracked).

Por defeito, o VS Code mistura tudo, o que é uma confusão.

![Painel de Controlo de Código do VS Code a mostrar ficheiros preparados, modificados e não rastreados todos misturados numa única lista.](/images/vscode/vscode-git-mixed-changes.png)

Para que ele mostre as alterações e os ficheiros novos separadamente (como deve ser), façam o seguinte:

1.  Pressionem `Ctrl + Shift + P` (ou `Cmd + Shift + P` no Mac).
2.  Escrevam e selecionem: `Preferences: Open Settings (UI)`.
3.  Pesquisem por: `Git: Untracked Changes`.
4.  Mudem a opção para `separate`.

```json
"git.untrackedChanges": "separate"
```

Agora, se forem ao separador de Source Control, verão os "Untracked Changes" numa lista separada, distinta dos ficheiros modificados. Muito melhor!

![Painel de Controlo de Código do VS Code a mostrar uma secção separada para Alterações Não Rastreadas, distinta das Alterações.](/images/vscode/vscode-git-separate-untracked.png)
