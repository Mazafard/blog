---
author: ["Maza Fard"]
date: '2025-11-22'
description: Uma conversa rápida sobre Kubernetes (K8s) e K9s, o que são e porque são espetaculares.
PTtags:
  - kubernetes
  - k8s
  - k9s
  - devops
  - ferramentas
PTcategories:
  - tecnologia
  - devops
PTseries:
  - ferramentas devops
ShowToc: true
weight: 1
title: Trabalhando com K8s e K9s
---

## Qual é a do Kubernetes (K8s)?

Então, vamos falar um pouco sobre o Kubernetes, ou K8s como toda a gente lhe chama. Pensem no K8s como o capitão do vosso navio de contentores. É basicamente uma plataforma open-source gigante que trata de toda a parte chata por vocês: fazer deploy, escalar e gerir as vossas apps em contentores. A Google começou isto há uns tempos e agora é basicamente o padrão em todo o lado.

### Por que é fixe

1.  **Escalabilidade:** Precisam de mais potência porque a vossa app está a bombar? O K8s cria mais instâncias automaticamente. O tráfego baixou? Ele reduz para poupar recursos. Super simples.
2.  **Auto-recuperação:** Esta é a minha parte favorita. Se um contentor falhar, o K8s simplesmente reinicia-o. Se um servidor (nó) morrer, ele move as vossas coisas para um saudável. É como ter um sistema que se arranja sozinho.
3.  **Corre em qualquer lado:** Seja no portátil, na cloud, ou num servidor qualquer na cave, o K8s não quer saber. Corre onde for preciso.
4.  **Comunidade Gigante:** Toda a gente usa, por isso há imensas ferramentas e plugins para fazer o que vocês quiserem.

## E o K9s?

Ok, o `kubectl` é ótimo, mas estar o dia todo a escrever comandos gigantes cansa depressa. É aqui que entra o K9s. É uma interface no terminal que torna a navegação nos clusters muito mais interessante, quase como um videojogo. Fica de olho no cluster em tempo real e deixa-vos interagir com tudo usando atalhos de teclado simples.

### Por que vão adorar o K9s

1.  **Super Rápido:** Acabou-se o escrever `kubectl get pods -n my-namespace` cinquenta vezes por dia. É só navegar com as setas e carregar no Enter.
2.  **Ação em Tempo Real:** Veem o que está a acontecer no momento. Se um pod estiver a falhar, veem logo ficar vermelho.
3.  **Mestres do Teclado:** Assim que apanharem o jeito aos atalhos, vão voar pelos recursos. A sensação é muito fluida.
4.  **Gestão Fácil:** Querem ver logs? Carreguem em `l`. Querem uma shell dentro do pod? Carreguem em `s`. Port-forwarding? `shift-f`. É assim tão simples.

## Começar Rápido

Querem ver a coisa a funcionar? Aqui ficam alguns comandos para começar.

Primeiro, vejam o que está a correr com o `kubectl`:

```bash
# Listar todos os pods
kubectl get pods

# Verificar os serviços
kubectl get svc
```

Agora, arranquem com o `k9s` (assumindo que já o instalaram):

```bash
k9s
```

Boom! Estão na matrix. Usem as setas para se moverem e carreguem em `?` para ver todos os atalhos fixes.

## Conclusão

Sinceramente, o K8s é uma máquina para gerir apps, mas o K9s é o que torna o trabalho realmente divertido. Poupa imenso tempo e dores de cabeça. Se ainda não experimentaram esta combinação, têm mesmo de testar!
