# Serviço-de-notificacão-simples com Kafka
---
# 🎯 Objetivo do Projeto
O principal objetivo deste projeto é aplicar os conhecimentos teóricos sobre Apache Kafka em uma arquitetura de microsserviços. O projeto simula o ciclo de vida completo de uma notificação:

**- Produtor (producer-notificacao)->** Recebe o evento, persiste o registro no MySQL (garantindo durabilidade) e, em seguida, envia a mensagem de forma assíncrona para o tópico do Kafka.

**- Consumidor (consumer-notificacao)->** Escuta o tópico, lê a mensagem e a processa, registrando o evento recebido no console.

O projeto foca em resolver desafios reais de comunicação assíncrona, como a serialização e desserialização de objetos DTOs entre serviços


# Desafios Enfrentados e Soluções
**Incompatibilidade de Pacotes (ClassNotFoundException)**

**🧠Desafio:** O Produtor e o Consumidor estão em projetos Docker separados, resultando em diferentes nomes de pacotes (ProducerNotificacao.comProducerNotificacao.DTO.NotificacaoDTO vs. ConsumerNotificacao.comconsumerNotificacao.DTO.NotificacaoDTO). O Spring Kafka, por padrão, tenta carregar a classe do Produtor no Consumer, o que causa o erro ClassNotFoundException.

**✅Solução:** Implementamos o Mapeamento de Tipos através da propriedade SPRING_KAFKA_CONSUMER_PROPERTIES_SPRING_JSON_VALUE_TYPE_MAPPINGS no Docker Compose. Essa técnica instruiu o JsonDeserializer a traduzir o nome do pacote de origem para o nome do pacote local, resolvendo o conflito.

**Reinício Teimoso do Consumidor**

**🧠Desafio:** Após aplicar a correção de mapeamento, o Consumidor continuava a falhar, pois ficava preso tentando reprocessar mensagens antigas e corrompidas, mesmo após limpar os volumes.

**✅Solução:** Foi necessário alterar o group-id do Consumer (de comconsumerNotificacaoGroup para comconsumerNotificacaoGroupV2). Isso forçou o Kafka a criar um novo registro de leitura, fazendo com que o Consumidor ignorasse os offsets antigos e começasse a ler apenas as novas mensagens (com as correções já aplicadas).

# Tecnólogias Utilizadas
- Java 17+
- SpringBoot
- Docker
- Kafka
- MYSQL
- Zookeper

# Como Executar o Projeto

**🛠️ Como Executar o Projeto :**

- Clone o repositório.

- Certifique-se de ter o Docker e o Docker Compose instalados e em execução.

- Na raiz do projeto, execute o comando para subir toda a infraestrutura e os serviços:

   **docker-compose up --build**

# Endpoints

Producer (API): http://localhost:8080/notificacao/testekafka


Consumer (Listener): http://localhost:8081 (Disponível apenas para inspeção, não possui endpoints externos).



