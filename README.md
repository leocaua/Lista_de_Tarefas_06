Lista de Tarefas 06 — Spring Boot + Vue.js
Este repositório é a entrega da atividade "Projeto Lista de Tarefas 06". O objetivo é uma aplicação Web Full Stack que integra um backend em Spring Boot e um frontend em Vue.js, abordando a configuração e a correção de problemas de comunicação entre eles (CORS).

🚀 Status do Projeto
Concluído: A aplicação está totalmente funcional. O frontend se comunica com o backend, lista, adiciona, deleta e atualiza tarefas. Os erros propositais do tutorial base foram identificados e corrigidos.

🔍 Análise e Correção de Erros
O projeto base apresentava desafios de comunicação entre o frontend e o backend, que se manifestavam como "Falha ao carregar tarefas" ou erros 404. A investigação revelou três problemas principais que foram corrigidos.

1. Erro: Disparidade de Porta (Erro de CORS / ERR_FAILED)

Problema: O console do navegador mostrou um erro de CORS (Access-Control-Allow-Origin) e 403 Forbidden . Isso ocorreu porque o backend (Spring) estava configurado para aceitar requisições apenas da porta http://localhost:5173, mas o servidor de desenvolvimento do Vue (npm run dev) iniciou em uma porta diferente (ex: http://localhost:5174).


Correção (Backend): O arquivo WebConfig.java foi atualizado para permitir a origem correta (a porta em que o Vue estava rodando).

Java

// Em: backend/src/main/java/br/com/tarefas/api/config/WebConfig.java
@Override
public void addCorsMappings(CorsRegistry registry) {
    registry.addMapping("/**") 
            // Correção: Porta atualizada para a porta real do frontend
            .allowedOrigins("http://localhost:5174") 
            .allowedMethods("GET", "POST", "PUT", "DELETE", "OPTIONS")
            .allowedHeaders("*")
            .allowCredentials(true);
}
2. Erro: URL Duplicada (Erro 404 Not Found)

Problema: Ao testar a API diretamente no navegador (ex: http://localhost:8088/api/tarefas), o servidor retornava um erro 404 (Not Found). Isso foi causado por uma duplicação no caminho da URL .



Configuração Global: application.properties definia um prefixo global: server.servlet.context-path=/api.


Configuração do Controller: TarefaController.java também definia o prefixo: @RequestMapping("/api/tarefas").


Resultado: A URL final no servidor era /api/api/tarefas.


Correção (Backend): O prefixo redundante foi removido do TarefaController.java, deixando o application.properties como a única fonte do /api .

Java

// Em: backend/src/main/java/br/com/tarefas/api/controller/TarefaController.java

// ANTES: @RequestMapping("/api/tarefas")
// CORREÇÃO:
@RequestMapping("/tarefas")
public class TarefaController {
    // ...
}
3. Erro: Falha na Configuração do Contexto (Erro 404)
Problema: Em determinado momento, a API inteira parou de funcionar em /api/tarefas, mesmo após a correção acima. O log do Spring Boot mostrou que o servidor iniciou com context path '/' (raiz), indicando que o application.properties não estava sendo lido.

Correção (Backend): A linha server.servlet.context-path=/api no arquivo application.properties estava comentada com um #. O comentário foi removido para ativar a configuração.

Properties

# Em: backend/src/main/resources/application.properties

# ANTES: # server.servlet.context-path=/api
# CORREÇÃO:
server.servlet.context-path=/api
🛠️ Como Executar o Projeto
Para executar esta aplicação, você precisará de dois terminais.

1. Backend (Spring Boot)
Abra a pasta backend no IntelliJ IDEA (ou sua IDE Java preferida).

Aguarde o Maven baixar as dependências.

Encontre e execute o arquivo principal ApiApplication.java.

O servidor estará rodando em http://localhost:8088.

2. Frontend (Vue.js)
Abra a pasta app-tarefas (ou frontend) no VS Code (ou seu editor de código preferido).

Abra um terminal dentro desta pasta.

Instale as dependências:

Bash

npm install
Instale o Axios:

Bash

npm install axios
Inicie o servidor de desenvolvimento:

Bash

npm run dev
O terminal mostrará a URL da aplicação (ex: http://localhost:5174/).

Abra esta URL no seu navegador.
