# Conciliação SLC — Batimento de Transações com a Nuclea

Aplicativo de automação para acompanhamento e conciliação dos arquivos SLC, cruzando as transações recebidas do arranjo de pagamentos com os respectivos retornos enviados pela Nuclea — substituindo um processo manual que consumia horas de análise por dia.

## Problema

A verificação de cada transação exigia um fluxo manual extenso:

- Acessar a pasta de rede dos arquivos recebidos e localizar o tipo de operação e a data desejada
- Consultar os arquivos até localizar a credenciadora correspondente
- Acessar a pasta de rede dos arquivos de retorno enviados pelo Banco
- Localizar o tipo de operação, a data e o arquivo correspondente
- Identificar o número sequencial equivalente entre o arquivo recebido e o de retorno
- Localizar manualmente a posição do `nu_liquid` para confirmar a correspondência da transação
- Consultar a posição do código de resposta no arquivo de retorno para identificar o status
- Consultar a posição da conta no arquivo recebido para obter os dados do cliente
- Repetir esse processo individualmente para cada transação

Com uma média diária de **~4.800 transações**, esse fluxo manual era operacionalmente pesado e aumentava a exposição a erros de conferência, perda de informação e inconsistência no cruzamento dos dados.

## Solução

O aplicativo automatiza a consolidação e o batimento dos diferentes arquivos recebidos do arranjo de pagamentos, cruzando-os com as respostas enviadas à Nuclea para identificar automaticamente a efetivação ou não de cada transação.

Como saída, gera uma planilha de extrato consolidada contendo:

- Arquivo de origem
- Data
- Credenciadora
- Dados do pagamento
- `nu_liquid`
- Código de resposta
- Status do retorno
- Informações da conta

## Ganhos

- Substituição da localização e conferência manual por processamento sistematizado
- Processamento de alto volume de transações (~4.800/dia) sem intervenção manual repetitiva
- Padronização das informações disponibilizadas para análise
- Redução da exposição a erros operacionais de conferência manual
- Maior agilidade na identificação do status de cada transação
- Centralização das informações em uma única planilha de extrato
- Rastreabilidade a partir dos arquivos de origem e de retorno

## Como funciona

O usuário escolhe, numa interface gráfica simples (Tkinter), o **tipo de operação** (Crédito, Débito ou Antecipação) e a **data** desejada. A partir daí, o aplicativo:

1. Localiza automaticamente os arquivos de origem (recebidos) e de retorno correspondentes àquele tipo e data, nas pastas de rede configuradas
2. Faz o parsing dos arquivos de layout fixo por posição de coluna (padrão SLC/Nuclea), extraindo cabeçalho, dados da conta e cada transação
3. Cruza as transações recebidas com os respectivos retornos pelo identificador `nu_liquid`
4. Traduz o código de resposta numérico para uma descrição legível de status (ex: "LANÇAMENTO EFETUADO", "CONTA BLOQUEADA", "LANÇAMENTO RECUSADO PELA CREDENCIADORA")
5. Gera automaticamente a planilha de extrato consolidada, pronta para consulta

## Destaques técnicos

- **Parsing de arquivo de largura fixa**: cada linha é interpretada por posição de caractere (ex: `linha[171:190]` para o valor), seguindo o layout oficial dos arquivos SLC — sem bibliotecas externas de parsing, direto em Python puro.
- **Import tardio do pandas**: o `pandas` só é importado quando o usuário efetivamente clica em processar, não na abertura do app — reduz o tempo de carregamento inicial da interface.
- **Interface sem depender de bibliotecas gráficas pesadas**: toda a tela (incluindo degradê de fundo e animação de abertura) é desenhada com `Canvas` do Tkinter puro, mantendo o executável leve.
- **Mapa de status de negócio**: os códigos de resposta trocados com a Nuclea são traduzidos para descrições legíveis, eliminando a necessidade de consulta manual a uma tabela de referência.
- **Tratamento de erro amigável**: identifica e orienta especificamente quando a falha é por o arquivo de destino estar aberto no Excel — um erro comum no uso real do dia a dia.

## Tecnologias

`Python` · `pandas` · `Tkinter` · `pathlib` · `glob`

---

⚠️ Os caminhos de rede originais foram substituídos por placeholders genéricos (`\\SEU_SERVIDOR\...`) para este repositório público.
