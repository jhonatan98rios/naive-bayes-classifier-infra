# Arquitetura

## [Publisher (Node)](https://github.com/jhonatan98rios/naive-bayes-classifier-publisher)
- Recebe a requisição com o arquivo de treino e dados do classifier
- Faz o upload do arquivo de treino
- Valida o arquivo
- Cria um documento no mongodb com os dados do classificador
- Publica evento no SQS


## Worker ([Python](https://github.com/jhonatan98rios/naive-bayes-classifier-worker-py) e [Node](https://github.com/jhonatan98rios/naive-bayes-classifier-worker))
- Escura a fila no SQS
- Lê os dados do evento com os dados do arquivo de treino
- Consome o arquivo de treino 
- Tenta:
    - Realiza o treinamento
    - Salva o treinamento em um modelo
    - Envia o modelo para o S3
    - Atualiza o mongodb com sucesso
- Caso falhe:
    - Atualiza o mongodb com falha


## Classifier ([Node](https://github.com/jhonatan98rios/naive-bayes-classifier-runner-ts), [Python](https://github.com/jhonatan98rios/naive-bayes-classifier-runner-py))
- Recebe a requisição com sample e id
- Lê o classifier do mongodb baseado no id
- Consome o modelo do S3 baseado no path presente no documento
- Recupera o algoritmo do arquivo
- Realiza a classificação
- Responde com a cçassificação


## [API (Node)](https://github.com/jhonatan98rios/naive-bayes-classifier-api)
- Lê os classifiers do mongodb

