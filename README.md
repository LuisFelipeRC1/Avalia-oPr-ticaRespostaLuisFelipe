# Desafio de Análise de Dados com Power BI

> **Repositório:** solução do desafio construída em Power BI a partir do arquivo `AvaliaçãoPráticaRespostaLuisFelipe.pbix`.
>
> **Objetivo:** explicar, de forma elegante e passo a passo, como construí a solução — do entendimento do problema à publicação — para que qualquer pessoa consiga reproduzir, avaliar e evoluir o projeto.

---

## 🧭 Sumário

* [Contexto do Desafio](#contexto-do-desafio)
* [Visão Geral da Solução](#visão-geral-da-solução)
* [Arquitetura & Fluxo de Trabalho](#arquitetura--fluxo-de-trabalho)
* [Como Reproduzir (Passo a Passo)](#como-reproduzir-passo-a-passo)
* [Preparação dos Dados (Power Query)](#preparação-dos-dados-power-query)
* [Modelagem & Relacionamentos](#modelagem--relacionamentos)
* [Métricas (DAX)](#métricas-dax)
* [Design do Relatório](#design-do-relatório)
* [Validação & Qualidade](#validação--qualidade)
* [Publicação & Compartilhamento](#publicação--compartilhamento)
* [Estrutura do Repositório](#estrutura-do-repositório)
* [Próximos Passos](#próximos-passos)
* [Como Contribuir](#como-contribuir)
* [Licença](#licença)

---

## 📌 Contexto do Desafio

Breve descrição do problema de negócio analisado e quais perguntas o dashboard responde.

**Exemplos de perguntas de negócio**

* Quais são os principais KPIs e seus drivers?
* Como evoluíram os resultados por período/segmento/região?
* Onde existem oportunidades de otimização?

> *Observação:* os detalhes específicos do enunciado do desafio podem ser adicionados aqui, mantendo dados sensíveis anonimizados quando necessário.

---

## 🧩 Visão Geral da Solução

* **Ferramenta principal:** Microsoft Power BI Desktop
* **Arquivo de entrega:** `AvaliaçãoPráticaRespostaLuisFelipe.pbix`
* **Abordagem:** *ELT leve* (carregamento dos dados brutos, transformação no Power Query, modelagem em estrela e medidas DAX de negócio)
* **Pilares:** clareza visual, consistência de métricas, performance e reprodutibilidade

**Destaques**

* Modelo em estrela com tabelas fato e dimensões
* Métricas DAX documentadas
* Navegação intuitiva (índice/atalhos/painel de filtros)
* Padrões de design consistentes (cores, tipografia, espaçamento)

---

## 🏗️ Arquitetura & Fluxo de Trabalho

```text
Fonte(s) de Dados → Power Query (limpeza/normalização) → Modelo (tabelas & relações) → DAX (medidas) → Páginas do relatório → Publicação/Compartilhamento
```

**Boas práticas adotadas**

* Nomes semânticos para tabelas/colunas/medidas
* Calendário único e marcado como tabela de datas
* Medidas base → medidas derivadas → medidas de exibição
* Separação entre camadas (Dados, Modelo, Visual)

---

## ▶️ Como Reproduzir (Passo a Passo)

1. **Clone o repositório**

   ```bash
   git clone https://github.com/<seu-usuario>/<seu-repo>.git
   cd <seu-repo>
   ```
2. **Abra o arquivo PBIX**

   * Requer **Power BI Desktop** (versão recente).
   * Dê duplo clique em `AvaliaçãoPráticaRespostaLuisFelipe.pbix`.
3. **Atualize as conexões** (se houver fontes externas)

   * `Página Inicial` → `Transformar dados` → `Parâmetros` → ajuste caminhos/credenciais.
4. **Reaplicar etapas do Power Query**

   * `Transformar dados` → confirme as etapas no painel **Consultas**.
5. **Verifique o modelo**

   * `Exibição de Modelo` → confira relacionamentos (direcionalidade e cardinalidade).
6. **Recalcule medidas**

   * Abra `Exibição de Relatório` e teste os visuais; valide números com amostras conhecidas.
7. **(Opcional) Publicar**

   * `Publicar` → escolha seu workspace no Power BI Service.

> Se os dados não estiverem no repositório (por sigilo), substitua por *arquivos exemplo* ou conecte a uma fonte pública/parametrizada.

---

## 🧹 Preparação dos Dados (Power Query)

**Objetivo:** padronizar tipos, tratar valores ausentes, corrigir chaves e normalizar dimensões.

**Etapas típicas aplicadas**

* Definição de **tipos de dados** corretos
* **Trim/clean** de texto, *case* consistente
* **Remoção de duplicidades** e linhas vazias
* **Divisão/mescla de colunas**, *unpivot* para formato longo
* Criação de **dimensões** (ex.: Datas, Produto, Cliente, Região)
* Padronização de **códigos/chaves** para relacionamentos robustos

> Todas as etapas ficam registradas no painel de etapas aplicadas para auditoria.

---

## 🧠 Modelagem & Relacionamentos

**Estratégia:** modelo em **estrela** com uma ou mais **tabelas fato** conectadas a **dimensões** por chaves substitutas.

**Boas práticas**

* **Tabela Calendário** (contínua, sem buracos; colunas: Ano, Mês, Trimestre, Mês-Ano, etc.)
* Relacionamentos **1:* (dim → fato)*\* e direção de filtro **single** quando possível
* Campos de **ordenação por coluna** (ex.: ordenar Mês por Nº do Mês)

> No PBIX, utilize a `Exibição de Modelo` para visualizar e editar as relações.

---

## 📏 Métricas (DAX)

Abaixo, a estrutura de medidas usada e exemplos genéricos (ajuste aos seus nomes/negócio):

```DAX
-- Pasta: _Base
Total Valor := SUM ( Fato[Valor] )
Total Quantidade := SUM ( Fato[Qtd] )

-- Pasta: _Cálculos
Ticket Médio := DIVIDE ( [Total Valor], [Total Quantidade] )
Meta Atingida % := DIVIDE ( [Total Valor], [Meta] )

-- Pasta: _Tempo
Valor MTD := TOTALMTD ( [Total Valor], 'Calendario'[Data] )
Valor YTD := TOTALYTD ( [Total Valor], 'Calendario'[Data] )

-- Pasta: _Comparação
Crescimento % YoY :=
VAR Atual = [Total Valor]
VAR AnoPassado = CALCULATE ( [Total Valor], SAMEPERIODLASTYEAR ( 'Calendario'[Data] ) )
RETURN DIVIDE ( Atual - AnoPassado, AnoPassado )
```

**Convenções**

* Prefixos de pastas de medidas (`_Base`, `_Tempo`, `_Exibição`)
* Medidas **não** em colunas calculadas quando dependem de filtro dinâmico

---

## 🎨 Design do Relatório

**Princípios**

* **Hierarquia visual** clara (título → KPIs → gráficos → tabela de detalhe)
* **Cores** com contraste adequado e paleta reduzida
* **Espaçamento** consistente e componentes alinhados em grade
* **Ícones** e *tooltips* explicativos

**Layout típico das páginas**

1. **Visão Geral (Resumo Executivo)** – KPIs, tendência principal, segmentações-chave
2. **Análises por Dimensão** – por Produto/Cliente/Região
3. **Tempo** – MTD/QTD/YTD, sazonalidade e comparativos
4. **Operacional/Detalhe** – tabela detalhada com busca/exportação

> Dica: utilize `Painel de Seleção` + `Painel de Livro` para navegação/estados de filtro.

---

## ✅ Validação & Qualidade

* **Conferência de totais** entre fontes e visualizações
* **Revisão de outliers** e valores ausentes
* **Teste de filtros/cruzamentos** (interações esperadas)
* **Performance**: desabilitar `Auto date/time`; preferir medidas a colunas calculadas; evitar iterações custosas

**Checklist**

* [x] Dimensões sem duplicidades
* [x] Tabela Calendário marcada como Data
* [x] Relacionamentos corretos e ativos
* [x] Medidas auditadas com amostras

---

## ☁️ Publicação & Compartilhamento

1. **Publicar** no Power BI Service (workspace do time)
2. **Configurar atualização** (gateway/credenciais se necessário)
3. **Permissões** de acesso (Viewer/Member/Contributor)
4. **Dashboards** e **Alertas** (opcional)

> Inclua aqui links do relatório publicado, se aplicável (ou *screenshots* em `/assets`).

---

## 🗂️ Estrutura do Repositório

```
.
├─ AvaliaçãoPráticaRespostaLuisFelipe.pbix
├─ assets/                # imagens e capturas de tela do relatório
├─ docs/                  # especificações, dicionário de dados, decisões
├─ scripts/               # (opcional) SQL/M/Python auxiliares
└─ README.md              # este arquivo
```

**Sugerido em `docs/`**

* `dicionario_dados.md` – definição de colunas, tipos e regras de negócio
* `decisoes_tecnicas.md` – trade-offs e alternativas consideradas
* `roadmap.md` – backlog e próximos passos

---

## 🚀 Próximos Passos

* [ ] Adicionar *screenshots* do relatório em `assets/`
* [ ] Incluir métricas DAX finais neste README
* [ ] Publicar *pbit* (template) para facilitar replicação
* [ ] Criar testes de regressão de métricas (ex.: via Deneb/Tabular Editor)

---

## 🤝 Como Contribuir

1. Faça um *fork*
2. Crie uma *branch*: `git checkout -b feat/minha-melhoria`
3. *Commit*: `git commit -m "feat: minha melhoria"`
4. *Push*: `git push origin feat/minha-melhoria`
5. Abra um **Pull Request**

> Use *conventional commits* para manter o histórico limpo.

---

## 📄 Licença

Este projeto está licenciado sob a **MIT License**. Veja o arquivo `LICENSE` para mais detalhes.

---

### 📎 Anexos & Notas

* Arquivo principal: `AvaliaçãoPráticaRespostaLuisFelipe.pbix`
* Caso precise de dados de exemplo para reprodução pública, inclua-os em `/data` (ou utilize parâmetros no Power Query para caminhos locais).

> **Dúvidas ou sugestões?** Abra uma *issue* no repositório ou entre em contato pelo e-mail do autor.
