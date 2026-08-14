# B4P 360 v5 — correções aplicadas

Versão interna: **3.5.0**. As mudanças foram feitas no `index.html`, sem alterar o nome do banco IndexedDB, a store ou o formato dos registros já salvos.

## 1. RBT12, sublimite e teto

```js
// [FIX 1] Conferir a vigência legal antes de publicar (LC 123/2006, arts. 3º e 13-A, e atos do CGSN aplicáveis ao período).
const LIMITES_SIMPLES = { sublimiteIcmsIss:3600000, teto:4800000, margemAlerta:0.10 };

// [FIX 1] RBT12 móvel: últimos 12 meses encerrados na competência mais recente, sempre somando todos os canais.
function calcularRbt12(notes,targetDoc){ /* ... */ }
```

O painel, o relatório HTML, a planilha e o resumo executivo agora mostram o RBT12, percentuais e distâncias. A soma usa somente itens classificados como Venda e não obedece ao filtro visual de marketplace.

## 2. CEST ausente ou inconsistente

```js
// [FIX 2] Detecta o mesmo NCM alternando entre CEST preenchido e vazio no conjunto analisado.
const porNcm=new Map();
// ... adiciona “CEST inconsistente para o mesmo NCM” ao item sem CEST.

// [FIX 2] O KPI conta o item real sem CEST quando há regra ST/SP vigente ou inconsistência interna do mesmo NCM.
function itemSemCestSinalizavel(r,ncmsComCest){ /* ... */ }
```

O contador deixou de depender apenas do alerta principal. Cada item vazio é contado quando o NCM tem regra ST paulista vigente ou quando o mesmo NCM aparece preenchido em outro item.

## 3. Operações FULL e lista fechada de vendas

```js
// [FIX 3] CFOPs que entram na receita, deduzem vendas ou representam somente movimentação do estoque FULL.
const CFOPS_VENDA = new Set(["5101","5102","5105","5106","6101","6102","6105","6106"]);
const CFOPS_DEVOLUCAO_VENDA = new Set(["1201","1202","2201","2202"]);
const CFOPS_FULL_SAIDA = new Set(["5905","6905","5906","6906","5949","6949"]);
const CFOPS_FULL_RETORNO = new Set(["1905","2905","1906","2906","1949","2949"]);
```

FULL tem precedência absoluta, não vira receita e não cai em “A classificar”. Natureza textual não transforma mais um CFOP fora da lista fechada em receita.

## 4. Período com fonte única

```js
// [FIX 4] Fonte única dos valores iniciais do período; depois da carga, config.ini/config.fim são a única fonte da interface e dos relatórios.
const PERIODO_PADRAO = { ini: "2025-06-01", fim: "2026-05-31" };
```

Cabeçalho, seletor, visão geral, dossiê e resumo usam `config.ini/config.fim`. A data-fim literal existe uma única vez no arquivo.

## 5. Documentos únicos e log de importação

```js
// [FIX 5] Documentos únicos são deduplicados pela chave; linhas de log permanecem como rastreabilidade da importação.
m.linhasLogImportacao=logsAnalise.length;
m.documentosUnicos=new Set([...activeNotes.map(docKey), ...activeCtes.map(/* chave CT-e */)]).size;
```

O KPI principal não infla na reimportação. O log continua mostrando cada processamento. O filtro abre em “Todos os marketplaces” e a origem de um novo lote exige escolha explícita.

## 6. Possível cadastro duplicado

```js
// [FIX 6] Mesma descrição e NCM com cProd distintos passa a gerar alerta cadastral.
if(codigos.size>1) flags.push(`Possível cadastro duplicado: códigos ${[...codigos].join(", ")}`);
```

A consolidação por produto cruza descrição normalizada e NCM, preservando os códigos separados e exibindo o alerta nos dois cadastros.

## 7. Produção própria x revenda

```js
// [FIX 7] Conjuntos específicos para verificar alternância entre produção própria e revenda.
const CFOPS_PRODUCAO = new Set(["5101","5105","6101","6105"]);
const CFOPS_REVENDA = new Set(["5106","6106"]);
```

Quando a mesma descrição/NCM ocorre nos dois grupos, a aba Produtos mostra “Divergência produção x revenda”.

## 8. Fornecedores e destinatários

```js
// [FIX 8] A contraparte depende da direção e o próprio CNPJ analisado nunca entra no contador.
if(papel==="entrada") m.fornecedores.add(/* contraparte */);
if(papel==="saida") m.destinatarios.add(/* contraparte */);
```

Entradas mostram fornecedores únicos; saídas mostram destinatários únicos. O CNPJ analisado é excluído dos dois conjuntos.

## 9. CNPJ real no cabeçalho

```js
// [FIX 9] O documento exibido vem do XML e recebe máscara somente para apresentação.
const fmtCnpj = v => { /* máscara 00.000.000/0000-00 */ };
```

Com XML carregado, o cabeçalho mostra o documento localizado. Sem documento carregado, mostra “CNPJ não informado”.

## 10. Abas responsivas

```css
/* [FIX 10] As abas permanecem inteiras e ganham rolagem horizontal suave no mobile. */
.tabs{flex-wrap:nowrap;overflow-x:auto;overscroll-behavior-inline:contain;-webkit-overflow-scrolling:touch;scroll-behavior:smooth}
.tab{white-space:nowrap;flex:0 0 auto}
```

O desktop conserva a quebra atual; em telas estreitas, a barra passa a rolar horizontalmente sem cortar a última aba.

## Compatibilidade e validação

- Banco IndexedDB e store mantidos, sem migração destrutiva.
- `schemaVersion` do backup mantido em 2.
- Dados sem origem gravada continuam sendo interpretados como Mercado Livre, preservando projetos antigos.
- Série, número e chave continuam preservados no log, nas evidências e nas exportações.
- JSX compilado com Babel 7.23.5.
- Testes dirigidos confirmaram os 8 CFOPs de venda, as 4 devoluções e os 12 CFOPs FULL.

## Pontos fiscais que exigem manutenção/validação do responsável

1. Confirmar, em cada exercício, os limites de R$ 3.600.000 e R$ 4.800.000 e os efeitos de excesso previstos na LC 123/2006 e nos atos do CGSN.
2. Manter atualizada e validar a lista de NCM/CEST efetivamente sujeitos a ICMS-ST em São Paulo, inclusive descrição, segmento e vigência.
3. Definir se o RBT12 operacional deve considerar ajustes adicionais que não constem como itens Venda nos XMLs (cancelamentos extemporâneos, descontos incondicionais, outras receitas ou conciliação com PGDAS-D/contabilidade).
4. Confirmar se CFOP 5102/6102 deve entrar também na comparação “produção própria x revenda”; a solicitação fornecida definiu apenas 5106/6106 como revenda para esse alerta específico.

Fontes oficiais consultadas: LC 123/2006, arts. 3º e 13-A (Planalto), e comunicado do Simples Nacional sobre o sublimite de 2026.
