# Análise Fiscal — B4P

Sistema estático de conferência documental e tributária desenvolvido para o projeto B4P.

Esta é a **publicação v1** do sistema, correspondente à versão interna **3.2.1**.

## O que o sistema faz

- importa arquivos XML avulsos ou em lote dentro de arquivos ZIP;
- identifica NF-e/NFC-e, eventos de cancelamento, inutilizações e CT-e;
- organiza os documentos por competência;
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
3. Importe cada categoria de documentos e valide o checklist automático.
4. Confira alertas, operações/CFOP, tributação e sequência fiscal.
5. Exporte o relatório robusto e o backup JSON antes de mudar de navegador ou limpar dados.

## Dependências

O `index.html` carrega React, Babel, SheetJS e JSZip pelo CDNJS. Por isso, o primeiro carregamento exige acesso à internet.

## Escopo

Esta versão é específica para a B4P e para o fluxo atual da consultoria. Ela não deve ser reutilizada para outro cliente sem revisão das configurações, regras fiscais, período analisado e critérios de receita.

## Licenciamento

Nenhuma licença de reutilização foi concedida. Todos os direitos permanecem reservados ao proprietário do projeto.
