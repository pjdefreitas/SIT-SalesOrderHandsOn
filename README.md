# Copiloto de Sales Order com GenAI no SAP BTP

Hands-on prático para demonstrar como construir uma **extensão Clean Core fora do SAP S/4HANA** usando **SAP Build Apps** integrada à **OpenAI API** para transformar dados técnicos de uma Sales Order em contexto de negócio.

A solução consulta uma **Sales Order real no S/4HANA via API**, interpreta os dados com IA e entrega ao usuário:

- **Resumo executivo** do pedido em linguagem humana
- **Riscos e inconsistências** observáveis
- **Pontos de atenção** com possíveis causas plausíveis
- **Corpo de e-mail** gerado automaticamente para comunicar o sucesso do hands-on

O objetivo é mostrar, na prática, como aplicar **GenAI em uma extensão no SAP BTP**, seguindo os princípios de **Clean Core** e criando uma experiência muito mais inteligente para o usuário final.

---

## Visão Geral

Esse projeto foi construído para um cenário de demonstração e hands-on no ecossistema SAP, conectando:

- **SAP Build Apps** como camada de experiência
- **SAP S/4HANA** como origem de dados reais de Sales Order
- **OpenAI Chat Completions API** como motor de interpretação e geração de texto
- **mailto** para facilitar o compartilhamento do resultado

A proposta do app é simples e forte:

> transformar dados técnicos do SAP em leitura de negócio e comunicação acionável, sem mexer no core do ERP.

---

## Caso de Uso

No dia a dia, uma Sales Order pode carregar vários sinais importantes:

- bloqueios
- status incompletos
- risco de atraso
- impacto de crédito
- pendências de entrega
- divergências entre pedido e execução

Normalmente, essas informações ficam espalhadas em campos técnicos e exigem leitura manual.

Com esse copiloto, o usuário consegue selecionar uma Sales Order e receber rapidamente uma análise mais inteligente do cenário, em linguagem de negócio.

---

## Objetivo do Hands-on

Demonstrar como criar uma aplicação no **SAP BTP** capaz de:

1. Consultar uma Sales Order real
2. Extrair os campos mais relevantes do pedido
3. Enviar o contexto para um modelo de IA
4. Gerar um insight estruturado
5. Gerar um corpo de e-mail automaticamente
6. Exibir tudo isso em uma experiência simples no **SAP Build Apps**

---

## Arquitetura da Solução

```text
SAP Build Apps
   |
   |-- Consumo da Sales Order via API / Integration
   |
   |-- HTTP Request -> OpenAI Chat Completions
   |      |
   |      |-- Prompt 1: Insight Estruturado
   |      |-- Prompt 2: Corpo de E-mail
   |
   |-- Save AI Return
   |
   |-- Exibição na interface
   |-- mailto para envio
```

### Componentes principais

- **SAP Build Apps**: interface e orquestração visual
- **API de Sales Order do SAP S/4HANA**: fonte dos dados
- **OpenAI API**: geração dos insights e do e-mail
- **Ação mailto**: envio rápido do resultado por e-mail

---

## Funcionalidades

### 1. Consulta de Sales Orders
A aplicação consome pedidos reais do SAP S/4HANA e exibe os principais dados para análise.

### 2. Insight Estruturado com IA
A IA interpreta os dados do pedido e retorna um insight com o seguinte formato:

```text
Resumo: [texto] ; Riscos: [texto] ; Atenção: [texto]
```

### 3. Geração de Corpo de E-mail
A aplicação também gera automaticamente um corpo de e-mail em português do Brasil para comunicar que o hands-on funcionou com sucesso.

### 4. Compartilhamento rápido
O texto gerado pode ser usado diretamente em uma ação `mailto:`.

---

## Dados Utilizados da Sales Order

Os prompts foram desenhados com base nos campos mais relevantes da entidade `A_SalesOrder1`.

### Campos principais

- `data.A_SalesOrder1.SalesOrder`
- `data.A_SalesOrder1.SalesOrderType`
- `data.A_SalesOrder1.SoldToParty`
- `data.A_SalesOrder1.TotalNetAmount`
- `data.A_SalesOrder1.TransactionCurrency`
- `data.A_SalesOrder1.SalesOrderDate`
- `data.A_SalesOrder1.RequestedDeliveryDate`
- `data.A_SalesOrder1.OverallSDProcessStatus`
- `data.A_SalesOrder1.OverallDeliveryStatus`
- `data.A_SalesOrder1.OverallTotalDeliveryStatus`
- `data.A_SalesOrder1.OverallOrdReltdBillgStatus`
- `data.A_SalesOrder1.TotalBlockStatus`
- `data.A_SalesOrder1.TotalCreditCheckStatus`
- `data.A_SalesOrder1.DeliveryBlockReason`
- `data.A_SalesOrder1.HeaderBillingBlockReason`
- `data.A_SalesOrder1.PurchaseOrderByCustomer`
- `data.A_SalesOrder1.CustomerPurchaseOrderDate`

Esses campos ajudam a compor uma leitura de negócio equilibrada entre:

- status operacional
- riscos
- contexto comercial
- bloqueios
- entrega
- faturamento
- crédito

---

## Acesso ao Ambiente

> **Atenção:** não publique credenciais reais em repositórios públicos. Usuário, senha e chaves de API devem ficar fora do Git ou em arquivos ignorados.

### SAP Build Apps

- **Link**: `https://1e7fd8fatrial.us10.build.cloud.sap/lobby`
- **Usuário**: `sit.handson@lab2learn.com.br`
- **Senha**: `Lab2learn#2026`

> Recomendação: para publicar este projeto, substitua esses dados por placeholders ou mova essas informações para documentação privada.

---

## HTTP Request

### Endpoint

```text
https://api.openai.com/v1/chat/completions
```

### Method

```text
POST
```

### Headers

```text
Content-Type: application/json
Authorization: Bearer [OPENAI_API_KEY]
```

> **Importante:** nunca commitar a chave real da OpenAI no repositório.

---

## Prompt 1 — Insight Estruturado

Esse prompt gera a leitura de negócio da Sales Order.

### Objetivo
Transformar dados técnicos do pedido em um insight claro, estruturado e acionável.

### Body

```json
{
  "model": "gpt-5.2",
  "messages": [
    {
      "role": "system",
      "content": [
        {
          "type": "text",
          "text": "Você é um copiloto de Sales Order especializado em SAP S/4HANA e atendimento ao cliente. Sua função é interpretar dados estruturados de um pedido e transformar informações técnicas em linguagem clara de negócio. Analise apenas os dados recebidos. Não invente fatos, não assuma causas sem evidências e não mencione nomes técnicos de campos no retorno. Retorne somente no formato solicitado, sem texto adicional."
        }
      ]
    },
    {
      "role": "user",
      "content": [
        {
          "type": "text",
          "text": "Analise a Sales Order abaixo e retorne somente uma linha no seguinte formato exato: Resumo: [texto] ; Riscos: [texto] ; Atenção: [texto]. Regras obrigatórias: resumo com no máximo 300 caracteres; riscos com no máximo 350 caracteres, listando apenas sinais concretos observáveis; atenção com no máximo 350 caracteres, trazendo possíveis causas plausíveis ou validações necessárias com base nos dados. Sem markdown, sem quebra de linha, sem explicações extras. Se precisar usar ponto e vírgula dentro do texto, substitua por vírgula. Dados da Sales Order: Pedido " + data.A_SalesOrder1.SalesOrder + ", tipo " + data.A_SalesOrder1.SalesOrderType + ", cliente " + data.A_SalesOrder1.SoldToParty + ", valor " + data.A_SalesOrder1.TotalNetAmount + " " + data.A_SalesOrder1.TransactionCurrency + ", data do pedido " + data.A_SalesOrder1.SalesOrderDate + ", entrega solicitada em " + data.A_SalesOrder1.RequestedDeliveryDate + ", status geral " + data.A_SalesOrder1.OverallSDProcessStatus + ", status de entrega " + data.A_SalesOrder1.OverallDeliveryStatus + ", status total da entrega " + data.A_SalesOrder1.OverallTotalDeliveryStatus + ", status de faturamento " + data.A_SalesOrder1.OverallOrdReltdBillgStatus + ", bloqueio total " + data.A_SalesOrder1.TotalBlockStatus + ", crédito " + data.A_SalesOrder1.TotalCreditCheckStatus + ", bloqueio de entrega " + data.A_SalesOrder1.DeliveryBlockReason + ", bloqueio de faturamento " + data.A_SalesOrder1.HeaderBillingBlockReason + ", pedido de compra do cliente " + data.A_SalesOrder1.PurchaseOrderByCustomer + ", data do pedido de compra do cliente " + data.A_SalesOrder1.CustomerPurchaseOrderDate
        }
      ]
    }
  ],
  "max_completion_tokens": 1000
}
```

---

## Prompt 2 — Corpo de E-mail

Esse prompt gera apenas o corpo do e-mail.

### Objetivo
Comunicar de forma objetiva e profissional que o hands-on funcionou com sucesso.

### Body

```json
{
  "model": "gpt-5.2",
  "messages": [
    {
      "role": "system",
      "content": [
        {
          "type": "text",
          "text": "Você é um assistente de comunicação profissional. Sua função é escrever apenas o corpo de e-mails em português do Brasil, de forma clara, objetiva e natural. Retorne somente o texto do corpo do e-mail, sem assunto, sem assinatura, sem markdown e sem qualquer explicação adicional."
        }
      ]
    },
    {
      "role": "user",
      "content": [
        {
          "type": "text",
          "text": "Escreva apenas o corpo de um e-mail profissional em português do Brasil informando que meu hands-on deu certo. Contexto do cenário: copiloto de Sales Order com GenAI no SAP BTP, usando SAP Build Apps. Use as informações abaixo para dar contexto ao texto. Pedido: " + data.A_SalesOrder1.SalesOrder + ". Cliente: " + data.A_SalesOrder1.SoldToParty + ". Tipo do pedido: " + data.A_SalesOrder1.SalesOrderType + ". Valor total: " + data.A_SalesOrder1.TotalNetAmount + " " + data.A_SalesOrder1.TransactionCurrency + ". Data do pedido: " + data.A_SalesOrder1.SalesOrderDate + ". Data solicitada de entrega: " + data.A_SalesOrder1.RequestedDeliveryDate + ". Status geral: " + data.A_SalesOrder1.OverallSDProcessStatus + ". Status de entrega: " + data.A_SalesOrder1.OverallDeliveryStatus + ". Status de faturamento: " + data.A_SalesOrder1.OverallOrdReltdBillgStatus + ". Bloqueio total: " + data.A_SalesOrder1.TotalBlockStatus + ". O e-mail deve comunicar de forma objetiva e positiva que a demonstração funcionou, que a aplicação conseguiu consultar uma Sales Order real, interpretar os dados e gerar insight de negócio, e que o resultado ficou muito bom para apresentação. Tom profissional, direto e levemente entusiasmado. Máximo de 900 caracteres."
        }
      ]
    }
  ],
  "max_completion_tokens": 800
}
```

---

## Save AI Return

Para salvar o retorno da IA no Build Apps, foi utilizada a seguinte expressão:

```text
STRING(outputs["HTTP request"].resBodyParsed.choices[0].message.content)
```

Essa expressão pega o conteúdo gerado pelo modelo e converte o resultado para string, facilitando a exibição e reutilização na interface.

---

## Fórmula `mailto`

A ação de envio por e-mail pode ser feita com a seguinte fórmula:

```text
"mailto:leandro.dante@lab2dev.com?subject=" + ENCODE_URL_COMPONENT("Dante deu certo meu HandsOn!") + "&body=" + ENCODE_URL_COMPONENT(pageVars.mensagem)
```

### Exemplo
Se a variável usada para armazenar o corpo do e-mail for `pageVars.mensagem`, essa fórmula já prepara:

- destinatário
- assunto
- corpo do e-mail

---

## Fluxo da Aplicação

### 1. Carregamento dos pedidos
O app busca uma coleção de Sales Orders no S/4HANA.

### 2. Seleção de um pedido
O usuário escolhe uma Sales Order para análise.

### 3. Geração do insight
O Build Apps executa um HTTP Request com o prompt de insight.

### 4. Salvamento do retorno
O resultado da IA é salvo com:

```text
STRING(outputs["HTTP request"].resBodyParsed.choices[0].message.content)
```

### 5. Geração do e-mail
Um segundo HTTP Request gera o corpo do e-mail com base no contexto do pedido.

### 6. Envio / compartilhamento
O usuário pode disparar o `mailto:` e revisar o conteúdo no cliente de e-mail.

---

## Boas Práticas Aplicadas

- **Clean Core**: nenhuma lógica foi embutida no core do S/4HANA
- **Uso de APIs padrão**: a aplicação consome dados por integração
- **IA como camada de interpretação**: a IA não substitui a fonte da verdade
- **Prompt enxuto e controlado**: os prompts limitam tamanho e formato do retorno
- **Separação de responsabilidades**:
  - prompt 1 para insight
  - prompt 2 para comunicação

---

## Limitações Atuais

- o modelo depende da qualidade e disponibilidade dos dados retornados pela Sales Order
- campos vazios ou nulos podem reduzir a qualidade do insight
- o formato textual com ` ; ` exige parsing simples na UI
- a chave da API precisa ser protegida adequadamente
- o retorno da IA é uma interpretação assistida, não uma decisão operacional automática

---

## Próximos Passos

Algumas evoluções possíveis para o projeto:

- listar também os **itens da Sales Order**
- incluir análise de **schedule lines**
- gerar **mensagem pronta para cliente**
- gerar **mensagem interna para time comercial / logística**
- classificar risco com semáforo
- trocar a chamada direta para uma camada intermediária em CAP
- armazenar histórico das análises
- suportar múltiplos idiomas

---

## Marketing / Posicionamento Lab2Learn

A **Lab2Learn** é uma referência no ecossistema SAP BTP no Brasil, com forte atuação em:

- capacitação
- hands-ons
- bootcamps
- imersões corporativas
- projetos de extensão
- Clean Core
- SAP BTP
- GenAI aplicada ao mundo SAP

Esse projeto reforça exatamente esse posicionamento:

> sair da teoria e mostrar, na prática, como usar SAP BTP + IA para gerar valor real em cenários de negócio.

A proposta do hands-on também conecta com a visão da Lab2Learn de tornar temas complexos mais acessíveis, aplicáveis e modernos para o mercado SAP.

### Canais da Lab2Learn

- **LinkedIn Dante**: https://www.linkedin.com/in/leandrodante/
- **Instagram Lab2Learn**: https://www.instagram.com/lab2learn.com.br
- **LinkedIn Lab2Learn**: https://www.instagram.com/lab2learn.com.br

> Observação: o link informado para “LinkedIn Lab2Learn” aponta para Instagram. Ajuste se quiser colocar o link correto da página no README.

---

## Como Rodar / Reproduzir

### Pré-requisitos

- acesso ao ambiente do SAP Build Apps
- acesso à integração / API de Sales Order
- chave válida da OpenAI API
- app configurado com os data resources e actions necessários

### Passos gerais

1. Acessar o SAP Build Apps
2. Abrir o projeto do copiloto
3. Validar a conexão com a origem de Sales Orders
4. Configurar o HTTP Request da OpenAI
5. Garantir o save do retorno em variável
6. Testar a geração de insight
7. Testar a geração do corpo do e-mail
8. Validar a ação `mailto`

---

## Segurança

### Não recomendado em repositório público

Nunca publicar diretamente:

- usuário e senha do ambiente
- API keys
- tokens de acesso
- segredos em headers

### Recomendado

- usar variáveis seguras
- usar documentação privada para credenciais
- rotacionar chaves caso tenham sido expostas
- remover segredos do histórico antes de publicar

---

## Sobre o Projeto

Esse repositório representa um hands-on prático de **SAP Build Apps + SAP S/4HANA + GenAI**, com foco em:

- experiência
- rapidez de prototipação
- Clean Core
- valor de negócio
- storytelling técnico para eventos e demonstrações

---

## Autor

**Leandro Dante Oliveira**  
Head of SAP BTP  
Lab2Dev / Lab2Learn

- LinkedIn: https://www.linkedin.com/in/leandrodante/

---

## Licença

Defina aqui a licença desejada para o repositório.

Exemplo:

```text
MIT
```

ou mantenha privado caso seja material interno de hands-on.