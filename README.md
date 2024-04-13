# Arquitetura

![Diagrama da arquietura](https://github.com/jhonatan98rios/naive-bayes-classifier-infra/blob/main/diagram.png?raw=true)

## [API (Spring)](https://github.com/jhonatan98rios/naive-bayes-classifier-api-spring)
#### Publisher
- Faz o upload do arquivo de treino
- Recebe a requisição com o arquivo de treino e dados do classifier
- Valida o arquivo
- Cria um documento no mongodb com os dados do classificador
- Publica evento no SQS

#### API
- Lista todos os classificadores criados
- Exibe um classificador por ID
- Exibe o status de um classificador por ID


## Worker ([Python](https://github.com/jhonatan98rios/naive-bayes-classifier-worker-py) e [Node](https://github.com/jhonatan98rios/naive-bayes-classifier-worker))
- Escuta a fila no SQS
- Lê os dados do evento com os dados do arquivo de treino
- Consome o arquivo de treino 
- Tenta:
    - Realiza o treinamento
    - Salva o treinamento em um modelo
    - Envia o modelo para o S3
    - Atualiza o mongodb com status de sucesso
- Caso falhe:
    - Atualiza o mongodb com status de falha


## Classifier ([Node](https://github.com/jhonatan98rios/naive-bayes-classifier-runner-ts) e [Python](https://github.com/jhonatan98rios/naive-bayes-classifier-runner-py))
- Recebe a requisição com sample e id
- Lê o classifier do mongodb baseado no id
- Consome o modelo do S3 baseado no path presente no documento
- Recupera o algoritmo do arquivo
- Realiza a classificação
- Responde com a classificação
