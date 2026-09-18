# Análise de PLN — Reação de torcedores às notas sobre "bets" no futebol brasileiro

Projeto de estudo em Processamento de Linguagem Natural (PLN) aplicado a comentários públicos do X (antigo Twitter), coletados a partir de um caso real e atual do futebol brasileiro.

> ⚠️ **Projeto de estudo/portfólio.** A amostra é pequena e não-aleatória (ver [Limitações](#limitações-e-avisos-importantes)). As conclusões aqui são exploratórias e não devem ser lidas como pesquisa de opinião pública representativa.

## Contexto

Em setembro de 2026, diversos clubes de futebol brasileiro publicaram notas oficiais defendendo o modelo regulado de apostas esportivas ("bets") em resposta a discussões sobre uma possível mudança na regulamentação do setor. Os clubes argumentaram que uma restrição abrupta ao mercado regulado comprometeria receitas, contratos e a saúde financeira de diversas agremiações — especialmente as menores.

Essas notas geraram forte reação nos comentários dos próprios posts. Este projeto usa esse episódio como estudo de caso para praticar um pipeline de PLN de ponta a ponta: coleta de dados reais, pré-processamento de texto, análise de frequência, análise de sentimento e extração de temas.

## Clubes / perfis analisados

| Clube / perfil | Post analisado |
|---|---|
| Flamengo | [x.com/Flamengo/status/2100707563805290612](https://x.com/Flamengo/status/2100707563805290612) |
| Sport Recife | [x.com/sportrecife/status/2100745240990584996](https://x.com/sportrecife/status/2100745240990584996) |
| Botafogo | [x.com/Botafogo/status/2100707104235417836](https://x.com/Botafogo/status/2100707104235417836) |
| Vila Nova FC | [x.com/VilaNovaFC/status/2100707900788232358](https://x.com/VilaNovaFC/status/2100707900788232358) |
| EC Juventude | [x.com/ECJuventude/status/2100724092865286397](https://x.com/ECJuventude/status/2100724092865286397) |
| Atlético Goianiense | [x.com/ACGOficial/status/2100715360886387149](https://x.com/ACGOficial/status/2100715360886387149) |
| Grêmio | [x.com/Gremio/status/2100719819595088157](https://x.com/Gremio/status/2100719819595088157) |
| São Paulo (perfil de torcida "Somos São Paulinos") | [x.com/somosaopaulinos/status/2100712960562004029](https://x.com/somosaopaulinos/status/2100712960562004029) |

> O último post não é da conta oficial do São Paulo FC — é de um perfil de torcedores comentando a nota conjunta dos clubes paulistas (FPF, São Paulo, Santos, Palmeiras, Corinthians). Isso está sinalizado na coluna `observacao` do dataset.

## O que o projeto faz

1. **Coleta de dados** — extração manual assistida de comentários públicos via navegador (o X não oferece mais acesso de leitura gratuito via API desde 2023; o modelo atual é pago).
2. **Pré-processamento** — normalização de texto em português: minúsculas, remoção de menções/URLs/pontuação, tokenização e remoção de stopwords (incluindo gírias e abreviações comuns em redes sociais como "pra", "vc", "q").
3. **Análise de frequência de palavras** — bag-of-words no corpus combinado.
4. **Análise de sentimento** — abordagem baseada em léxico PT-BR (dicionários de palavras positivas/negativas), já que o ambiente de execução não teve acesso de rede para baixar modelos pré-treinados (ex. BERTimbau).
5. **Extração de temas** — agrupamento manual de palavras-chave em temas (apostas/bets, dinheiro/lucro, vício/família, história do futebol, linguajar ofensivo), visualizado como heatmap clube × tema.
6. **Visualização** — gráficos com paleta validada para acessibilidade/daltonismo (ver seção [Visualização de dados](#visualização-de-dados)).

## Principais resultados

- Em **todos os 8 clubes/posts analisados**, a reação predominante nos comentários foi **negativa** em relação à posição oficial do clube — nenhum apresentou saldo de sentimento positivo.
- O tema mais recorrente, na maioria dos clubes, não foi o argumento financeiro usado nas notas oficiais, e sim o **impacto social do vício em apostas** sobre famílias.
- Clubes em situação esportiva ruim no momento da coleta atraíram uma camada extra de crítica ligada a desempenho em campo, somada à crítica sobre bets.
- Identificado um padrão de **comentários replicados** entre posts de clubes diferentes (mesmo autor/texto, adaptando apenas o nome do clube) — possível indício de usuários muito engajados no tema republicando a mesma mensagem.

Detalhes completos, com gráficos e números por clube, estão no notebook.

## Estrutura do repositório

```
.
├── comentarios_8_clubes_bets.csv     # dataset final (379 comentários, 8 clubes)
├── analise_pln_8_clubes.ipynb        # notebook com o pipeline completo e resultados
└── README.md
```

### Dicionário de dados (`comentarios_8_clubes_bets.csv`)

| Coluna | Descrição |
|---|---|
| `clube` | Clube/perfil ao qual o post pertence |
| `post_url` | URL do post original no X |
| `autor` | Nome de exibição do autor do comentário |
| `handle` | @handle do autor |
| `comentario` | Texto do comentário (vazio quando o comentário era só uma reação em imagem/vídeo, sem texto) |
| `comentario_oficial_do_clube` | `True` quando a linha é uma continuação da nota oficial do clube (thread), não um comentário de torcedor |
| `observacao` | Notas de contexto (ex.: comentário replicado em outro post, teor político, texto parafraseado) |

## Limitações e avisos importantes

- **Amostra pequena e não-aleatória.** O X ordena respostas por relevância/engajamento — a amostra favorece comentários com mais curtidas, não é uma amostra aleatória de todos os comentários do post.
- **Amostra desbalanceada entre clubes.** Clubes com posts mais virais (Flamengo, Sport Recife, Grêmio) têm 95 a 108 comentários; clubes menores/menos engajados (Vila Nova, EC Juventude, Atlético Goianiense) têm apenas 15 a 25.
- **Coleta manual.** Sujeita a pequenos erros de transcrição; não captura texto embutido em imagens/vídeos.
- **Análise de sentimento por léxico**, não por modelo treinado — não entende negação, sarcasmo/ironia (frequentes nesses comentários), e pode superestimar negatividade quando o xingamento é dirigido a terceiros (diretoria, governo) e não ao tema das bets em si.
- **Não é uma pesquisa de opinião pública.** Os resultados não devem ser generalizados para a torcida como um todo ou para a opinião pública brasileira sobre o tema.

Para uma análise com validade estatística maior, os próximos passos seriam: coleta via API oficial paga (amostra maior e sistemática), validação manual de uma sub-amostra da classificação de sentimento, uso de um modelo de linguagem pré-treinado em português, e modelagem de tópicos estatística (LDA/BERTopic) com maior volume de dados.

## Tecnologias usadas

- Python (pandas, numpy, matplotlib)
- Jupyter Notebook
- PLN: pré-processamento e análise de sentimento baseada em léxico, implementados do zero

## Autor

**Ítalo Silva (Budah)** — Especialista de Dados, MBA em Data Science & Analytics (USP/Esalq).

- LinkedIn: [linkedin.com/in/italosilvadata](https://linkedin.com/in/italosilvadata)
- GitHub: [github.com/italosilva02](https://github.com/italosilva02)

---

*Projeto desenvolvido para fins de estudo e portfólio.*
