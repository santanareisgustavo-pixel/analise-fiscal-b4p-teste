# B4P 360 v6 — módulo Conciliação PGDAS-D × Notas Fiscais

Versão interna: **3.6.0**. O módulo foi acrescentado ao `index.html` sem alterar o banco IndexedDB, a store ou o `schemaVersion` dos backups existentes.

## 1. Contexto fiscal fixo e parâmetros

```js
// [PGDAS] Contexto fixo da empresa: competência e uma única segregação no Anexo II.
const PGDAS_PADRAO = { toleranciaReais:0, toleranciaPercentual:0.5, fonteRbt12:"apurada" };
const PGDAS_SEGREGACAO = "Venda de mercadorias industrializadas pelo contribuinte, exceto para o exterior — sem ST, monofasia ou antecipação com encerramento (Anexo II)";
```

Não foi criado alternador de caixa. A segregação é apenas informativa e fixa, sem possibilidade de seleção de outro anexo.

## 2. Apuração e conciliação por competência

```js
// [PGDAS] Conciliação única do Anexo II, por competência de emissão e com base em vProd.
function montarConciliacaoPgdas(notes,targetDoc,config,pgdasValores,pgdasConfig){
  // receita apurada = vendas − devoluções da mesma competência
}
```

O motor cria uma linha para cada mês do período configurado, inclusive meses sem nota. As notas canceladas já chegam excluídas pelo filtro central; movimentações FULL não são classificadas como venda.

## 3. Valores declarados, diferenças, tolerância e risco

```js
function statusPgdas(apurada,declarada,preenchida,cfg){
  const limite=Math.max(
    parseMoedaBr(cfg.toleranciaReais),
    Math.abs(apurada)*(Number(cfg.toleranciaPercentual)||0)/100
  );
  // Conciliado, Divergente ou Pendente
}
```

- Diferença em reais: declarada menos apurada.
- Diferença percentual: diferença dividida pela apurada.
- Com base zero, o percentual fica como `N/A`, exceto quando ambos os valores são zero.
- A tolerância aplicada é o maior valor entre o limite em reais e o limite percentual.
- Diferença negativa divergente: possível omissão de receita.
- Diferença positiva divergente: declarou a maior, verificar ou retificar.

## 4. Alerta de CFOP de revenda

```js
if(op.key==="receita"&&CFOPS_REVENDA.has(onlyDigits(it.cfop))){
  g.revendaItens++;
  g.revendaNotas.add(docKey(n));
  g.revendaValor+=v;
}
```

Os CFOPs 5106/6106 continuam compondo a receita, mas recebem o aviso: **“CFOP de revenda em operação de indústria: revisar emissão”**. O painel mostra itens, notas e valor por competência e no total.

## 5. Persistência e backups antigos

```js
// [PGDAS] Novos campos são opcionais no projeto; backups antigos carregam os padrões sem migração destrutiva.
const [pgdasValores,setPgdasValores]=useState({});
const [pgdasConfig,setPgdasConfig]=useState(PGDAS_PADRAO);
```

`pgdasValores` e `pgdasConfig` foram incluídos no mesmo objeto já salvo pelo sistema no navegador e no backup JSON. Se esses campos não existirem em um projeto antigo, o sistema carrega `{}` e os padrões, sem bloquear a restauração.

## 6. Exportações e dossiê

```js
// [PGDAS] A conciliação e seus parâmetros integram o dossiê, sem depender do filtro visual de marketplace.
const conciliacaoPgdas = d.conciliacaoPgdas.linhas.map(/* todas as colunas */);
```

Foram incluídos botões CSV/XLSX no painel, a aba `19_Conciliacao_PGDAS` na planilha analítica, uma seção no relatório HTML e os totais no resumo executivo TXT.

## 7. Integração com RBT12 e sublimite

```js
// [PGDAS] No modo “apurada”, o RBT12 usa a mesma base líquida da conciliação: vendas menos devoluções.
function calcularRbt12ApuradaPgdas(dados){ /* ... */ }

// [PGDAS] Alternativa declarada do RBT12, usando somente competências efetivamente preenchidas.
function calcularRbt12Declarada(pgdasValores,referencia){ /* ... */ }
```

O padrão é a receita apurada pelas notas. O usuário pode selecionar a receita declarada; nesse caso, o painel informa quantas competências foram preenchidas e alerta enquanto houver menos de 12 meses.

## 8. Interface

```jsx
{tab==="pgdas" && <ConciliacaoPgdas
  dados={conciliacaoPgdas}
  pgdasValores={pgdasValores}
  setPgdasValores={setPgdasValores}
  pgdasConfig={pgdasConfig}
  setPgdasConfig={setPgdasConfig}
/>}
```

A nova aba possui totalizadores, semáforo, parâmetros, tabela mensal, observações e alerta cadastral, preservando o padrão visual do app.

## Definições ainda abertas

1. Confirmar a tolerância definitiva. Foi adotado o padrão de **R$ 0,00 ou 0,5%**, prevalecendo o maior valor.
2. Confirmar se o alerta de 5106/6106 deve permanecer informativo, como implementado, ou impedir o fechamento da competência.
3. Confirmar se o RBT12 declarado incompleto pode ser utilizado operacionalmente ou se a seleção deve ser bloqueada até existirem 12 competências preenchidas. Nesta versão ele é permitido, mas exibe alerta explícito.
