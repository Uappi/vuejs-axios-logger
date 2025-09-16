# Vue Axios Logger - Aplicações Vue.js com Nuxt

Esse plugin para Nuxt cria um log de requests enviadas pelo Axios dentro do Nuxt, seja pelo SSR ou pelo client. 
Isso é necessario pois as requests enviadas pelo client são fáceis de monitorar, porém as enviadas pelo SSR ficam mais ocultas e dificultam a análise de performance ou até de requisições duplicadas.

**DICA**: Utilize esse log para analisar as requests e se existem oportunidades de cache ou até de melhoria de fluxo para evitar chamadas desnecessárias às APIs backend.

**IMPORTANTE**: Não publique essa atualização em produção, visto que isso aumentará a quantidade de logs gerados pelo Nuxt e diminuirá a performance da aplicação.

Siga os passos abaixo para criação do log:

## 1. Crie o plugin plugins/axios-logger.js

```javascript
export default function ({ $axios, req }) {
  // Verifica se está no SSR (ambiente Node)
  const isServer = process.server;

  $axios.onRequest(config => {
    const context = isServer ? '[SSR Request]' : '[Client Request]';
    console.log(`${context} ${config.method.toUpperCase()} ${config.url}`);
  });

  $axios.onResponse(response => {
    const context = isServer ? '[SSR Response]' : '[Client Response]';
    console.log(`${context} ${response.status} ${response.config.url}`);
  });

  $axios.onError(error => {
    const context = isServer ? '[SSR Error]' : '[Client Error]';
    if (error.response) {
      console.error(`${context} ${error.response.status} ${error.response.config.url}`);
    } 
    else {
      console.error(`${context} ${error.message}`);
    }
  });
}
```

## 2. Registre o plugin em nuxt.config.js
Apenas adicione  o item `'~/plugins/axios-logger.js'` no array de plugins já existente.
```javascript
export default {
  plugins: [
    '~/plugins/axios-logger.js'
  ]
}
```

## 3. Executar o Nuxt no modo SSR
Se estiver usando o Nuxt com target: 'server' (o padrão), isso já é suficiente. Basta rodar:
```shell
npm run dev
```

O log exibido no console do terminal será algo parecido com isso:
```
[SSR Request] GET/posts
[SSR Response] 200 /posts
```

Já no console do navegador, algo como:
```
[Client Request] GET /posts
[Client Response] 200 /posts
```