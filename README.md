# B4P 360 — Diagnóstico Tributário

Sistema estático de apoio à consultoria tributária da B4P, com análise documental, identificação de riscos e mapeamento de oportunidades.

Esta é a **publicação v4** do sistema, correspondente à versão interna **3.4.0**.

Nesta versão, o sistema inicia vazio e não inclui documentos, valores ou controles de demonstração. Todos os indicadores passam a refletir exclusivamente os arquivos efetivamente importados pelo usuário.

A v4 também separa os documentos por marketplace. Os dados já existentes são migrados para **Mercado Livre** e cada novo lote deve ter sua origem escolhida antes da importação, incluindo **TOK&STOK**. A série fiscal permanece vinculada à nota e aparece nos controles e nas exportações.

## O que o sistema faz

- importa arquivos XML avulsos ou em lote dentro de arquivos ZIP;
- identifica NF-e/NFC-e, eventos de cancelamento, inutilizações e CT-e;
- organiza os documentos por competência;
- registra e filtra a origem de cada lote (Mercado Livre, TOK&STOK ou outros canais);
- preserva série, número e chave fiscal nos controles e relatórios;
- mantém checklist automático de documentos importados;
- analisa operações, CFOP, produtos e tributação;
- controla a sequência fiscal dos documentos emitidos pela empresa analisada;
- gera relatórios, planilhas, resumo executivo e backup restaurável do projeto.

## Privacidade e armazenamento

O processamento ocorre no navegador. Os XMLs selecionados não são gravados neste repositório nem enviados ao GitHub Pages pelo sistema.

Os dados processados ficam armazenados no **IndexedDB do navegador**, vinculados ao endereço publicado. Portanto:

- outro navegador ou computador não recebe automaticamente os dados já importados;
- limpar os dados do navegador pode apagar o projeto salvo;
- o backup JSON do sistema deve ser exportado regularmente;
- os XMLs originais devem continuar arquivados fora do repositório.

Nunca publique XMLs fiscais, backups JSON, relatórios exportados ou documentos do cliente neste repositório público.

## Executar pelo GitHub Pages

1. Envie os arquivos deste pacote para a raiz do repositório.
2. Abra **Configurações** do repositório.
3. Entre em **Páginas**.
4. Em **Fonte**, selecione **Implantar de uma ramificação**.
5. Escolha a ramificação **main** e a pasta **/(raiz)**.
6. Clique em **Salvar** e aguarde a publicação.

O GitHub mostrará o endereço público do sistema na própria tela de Páginas.

## Uso recomendado

1. Confirme o CNPJ e as regras na aba **Configurações**.
2. Selecione a competência que será conferida.
3. Antes de cada importação, selecione o marketplace de origem do lote.
4. Importe cada categoria de documentos e valide o checklist automático.
5. Confira alertas, operações/CFOP, tributação e sequência fiscal.
6. Exporte o relatório robusto e o backup JSON antes de mudar de navegador ou limpar dados.

## Dependências

O `index.html` carrega React, Babel, SheetJS e JSZip pelo CDNJS. Por isso, o primeiro carregamento exige acesso à internet.

## Escopo

Esta versão é específica para a B4P e para o fluxo atual da consultoria. Ela não deve ser reutilizada para outro cliente sem revisão das configurações, regras fiscais, período analisado e critérios de receita.

## Licenciamento

Nenhuma licença de reutilização foi concedida. Todos os direitos permanecem reservados ao proprietário do projeto.
