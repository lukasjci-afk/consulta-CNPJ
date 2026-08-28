# Consulta Simples Nacional — Transportes ARI

Ferramenta de campo da **Transportes ARI** para verificar o enquadramento de empresas no **Simples Nacional** a partir do CNPJ. Roda inteiramente no navegador (página estática no GitHub Pages) — **nenhum dado sai para servidor da ARI**.

Link público: `https://lukasjci-afk.github.io/consulta-cnpj/`
*(ajuste conforme o nome real do repositório)*

---

## O que faz

A página tem duas abas:

### 🔎 CNPJ avulso
Digite um CNPJ e receba na hora um cartão com os dados da empresa:
- Razão social e nome fantasia
- **Regime tributário** em destaque: Simples Nacional, Simples Nacional (MEI), Lucro Presumido ou Lucro Real
- Se optante ou não pelo Simples Nacional
- Situação cadastral, data de abertura, porte, município/UF, natureza jurídica e atividade principal (CNAE)

Aceita o CNPJ com pontuação ou só números (máscara automática) e busca com a tecla Enter.

### 📄 Planilha em lote
Envie um arquivo `.xls` ou `.xlsx` com uma coluna chamada **CNPJ**. A ferramenta consulta todos e devolve uma planilha idêntica (`RESULTADO_cnpjs.xlsx`) com as colunas **Status Simples Nacional** e **Regime Tributário** acrescentadas. Tem cache interno (não repete consulta de CNPJ igual) e um console que mostra o progresso linha a linha.

---

## Como funciona

Toda a lógica está em um único arquivo `index.html`. As consultas são feitas, em ordem de tentativa, a três fontes públicas:

1. **BrasilAPI** — `https://brasilapi.com.br/api/cnpj/v1/{cnpj}` (fonte principal, dados completos)
2. **MinhaReceita** — `https://minhareceita.org/{cnpj}` (reserva)
3. **ReceitaWS** — `https://receitaws.com.br/v1/cnpj/{cnpj}` (última reserva; limite de 3 consultas/minuto)

A leitura e a geração das planilhas usam a biblioteca **SheetJS** (carregada via CDN).

---

## Arquivos do repositório

| Arquivo | Função |
|---|---|
| `index.html` | A aplicação inteira (interface + lógica) |
| `capa.png` | Cartão de preview 1200×630 exibido quando o link é compartilhado |
| `README.md` | Este arquivo |

---

## Como publicar (GitHub Pages)

1. Crie um repositório novo e suba `index.html`, `capa.png` e `README.md` na **raiz**.
2. No repositório: **Settings → Pages → Source: Deploy from a branch → Branch: `main` / `root` → Save**.
3. Em ~1 minuto o link público fica no ar.

### Ajustar o cartão de preview (capa)
No topo do `index.html` há um bloco de meta tags marcado por comentário. Nas **3 URLs** (`og:image`, `og:url` e a base), troque `consulta-cnpj` pelo nome real do seu repositório, para o WhatsApp encontrar a `capa.png`.

```html
<meta property="og:image" content="https://lukasjci-afk.github.io/SEU-REPO/capa.png">
<meta property="og:url"   content="https://lukasjci-afk.github.io/SEU-REPO/">
```

### Preview antigo no WhatsApp?
O WhatsApp guarda a prévia em cache. Se aparecer a capa errada (ou nenhuma), acrescente um número novo no fim do link para forçar a atualização: `...github.io/SEU-REPO/?v=10`, depois `?v=25`, e assim por diante. Cada número novo obriga o WhatsApp a buscar do zero. O link com `?v=` funciona igual ao normal.

---

## Observações

- A consulta pela aba avulsa e em lote depende de a API liberar chamada direta do navegador (CORS). A **BrasilAPI** libera, então ela sustenta o funcionamento; as demais atuam como reserva.
- Para listas muito grandes (centenas de CNPJs), o ritmo é limitado de propósito (~1,2 s entre consultas novas) para respeitar os limites das APIs.
- O **regime tributário** (Lucro Real/Presumido) vem do campo `regime_tributario` da BrasilAPI, referente ao último ano-base declarado à Receita (dados de 2016 em diante). Quando a Receita não tem esse dado para o CNPJ, a ferramenta informa isso em vez de supor.
- A ferramenta não tem vínculo com a Receita Federal; as informações retornadas são das APIs públicas citadas.
