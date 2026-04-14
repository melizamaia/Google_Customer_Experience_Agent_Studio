# Maia Demo — Guia de Configuração

Este guia explica como configurar o agente Maia no **CX Agent Studio** (Dialogflow CX) e integrá-lo ao arquivo `maia-demo.html`.

---

## 1. Ativar a Dialogflow API no Google Cloud

Acesse o console do Google Cloud e abra o menu sanduíche.

![Menu sanduíche](assets/img_001.png)

Navegue até **APIs & Services**.

![APIs e Services](assets/img_002.png)

Localize e ative a **Dialogflow API**.

> O CX Agent Studio usa a Dialogflow API por baixo — é ela que precisa estar ativa.

![Dialogflow API](assets/img_004.png)

Clique em **Ativar**.

![Ativar](assets/img_005.png)

---

## 2. Acessar o CX Agent Studio

Acesse: [https://ces.cloud.google.com/projects](https://ces.cloud.google.com/projects)

Escolha o projeto desejado.

![Selecionar projeto](assets/img_006.png)

Clique em **Agree and continue**.

![Agree and continue](assets/img_007.png)

---

## 3. Criar o Agente

Clique em **Create your Agent IA**.

![Create Agent IA](assets/img_008.png)

Selecione **Create Your First AI Agent**.

![Create First Agent](assets/img_009.png)

Nomeie o agente como **Maia**.

![Agente Maia](assets/img_010.png)

Aguarde alguns minutos enquanto o agente é provisionado.

![Aguardando](assets/img_011.png)

> **Orientação sobre a tela principal:**
> - **Root agent** (centro) — é o coração do agente
> - **Preview agent** (esquerda) — onde você testa a conversa
> - **Build / Test / Deploy** (topo) — abas de navegação

---

## 4. Adicionar Instruções ao Root Agent

Clique em **Add instructions** dentro do card do Root agent.

![Add instructions](assets/img_0012.png)

Cole o seguinte prompt:

```xml
<role>
Você é a Maia, assistente virtual de suporte ao cliente da Melzitos.
Seu tom é cordial, direto e profissional.
Responda sempre em português brasileiro.
</role>

<persona>
Seja simpática mas objetiva. Respostas curtas e claras.
Se o cliente informar o nome, use-o na conversa.
</persona>

<goals>
- Responder dúvidas sobre produtos, pedidos e prazos
- Informar políticas de atendimento
- Encaminhar para humano quando necessário
</goals>

<knowledge>
- Prazo de entrega padrão: 5 a 7 dias úteis
- Política de troca: até 30 dias após a compra com nota fiscal
- Horário de atendimento humano: segunda a sexta, das 9h às 18h
- Suporte técnico: suporte@melzitos.com.br
</knowledge>

<constraints>
- Nunca invente informações que não estão no knowledge
- Se não souber responder, diga: "Vou te transferir para um especialista."
- Nunca revele detalhes internos da empresa
</constraints>
```

Clique em **Create**.

![Create](assets/img_013.png)

---

## 5. Testar o Agente

Abra o **Preview Agent** na lateral esquerda.

![Preview Agent](assets/img_014.png)

Digite as mensagens abaixo uma por uma e observe as respostas:

```
Olá, meu nome é Mel
Qual o prazo de entrega?
Posso trocar um produto?
Quero falar com um humano
```

> **Erro RESOURCE_EXHAUSTED?**
> A conta gratuita tem limite de chamadas por minuto ao Gemini. Aguarde 1–2 minutos e a cota se renova automaticamente.

![Erro de cota](assets/img_015.png)

---

## 6. Entendendo os Steps (Rastreabilidade)

Cada mensagem passa por 3 etapas visíveis em tempo real:

![Steps](assets/img_016.png)

| Etapa | Tempo | Descrição |
|---|---|---|
| Received user message | ~0.001s | O agente recebeu a mensagem |
| AI reasoning | ~0.570s | O Gemini lê as instruções e decide a resposta |
| Agent sent response | ~0.001s | A resposta foi entregue |

Toda conversa também é registrada em **JSON estruturado**, permitindo integração com sistemas externos, histórico no BigQuery e auditoria completa.

![JSON](assets/img_017.png)

---

## 7. Adicionar Sub-agente (Arquitetura Multi-agente)

No card do Root agent, clique no **"+"** na parte inferior → **Add sub-agent**.

![Add sub-agent](assets/img_018.png)

Confirme em **Create Anyway**.

![Create Anyway](assets/img_019.png)

> Aparecerá um aviso informando que criar um sub-agente desativa o Gemini helper para avaliações automáticas. Isso é esperado.

![Aviso sub-agente](assets/img_020.png)

Clique nos três pontinhos (⋮) do novo card → **Edit config** e configure:

- **Agent name:** `agente_faq`
- **Description:** `Especialista em perguntas frequentes sobre produtos e serviços`

Clique em **Save**.

![Edit config](assets/img_021.png)

![Config nome](assets/img_022.png)

![Salvar config](assets/img_023.png)

---

## 8. Configurar Delegação no Root Agent

Abra as **Instructions** do Root agent.

![Instructions root](assets/img_024.png)

Adicione ao final do prompt existente:

```
- Para perguntas sobre produtos, garantia e frete, delegue para {@AGENT: agente_faq}
```

Salve.

![Salvar instrução](assets/img_025.png)

### Teste da Delegação

Digite no Preview:

```
Quais produtos vocês vendem?
```

![Teste delegação](assets/img_026.png)

Você verá:
- **Handoff to agente_faq** — o Root agent delegou automaticamente para o sub-agente
- **5 Steps** — mais etapas porque passou por dois agentes
- Resposta correta em português

---

## 9. Recursos Avançados (visão geral)

| Recurso | Descrição |
|---|---|
| **Tools** | Conecta APIs externas (ERP, CRM). `end_session` encerra a conversa; `customize_response` personaliza voz |
| **Variables** | Armazena dados da sessão (ex: `{customer_name}`) |
| **Rich Response Widgets** | Templates visuais — carrossel de produtos, resumo de pedido, comparação |
| **Guardrails** | Prompt Guard, Blocklist e Safety — proteção contra manipulação |
| **Versions** | Histórico completo com data, hora e autor — permite rollback |
| **Settings Basic** | Modelo Gemini 2.5 Flash (automático), idioma padrão |
| **Settings Advanced** | Voz para telefonia, timeout de silêncio, DTMF |

---

## 10. Deploy e Integração com o maia-demo.html

Acesse a aba **Deploy**.

![Deploy](assets/img_027.png)

Escolha a opção de widget recomendada para demonstração.

![Widget](assets/img_028.png)

![Deploy config](assets/img_029.png)

![Deploy config 2](assets/img_030.png)

![Deploy config 3](assets/img_031.png)

![Deploy config 4](assets/img_032.png)

Copie o código gerado — ele é o script de integração do chat.

![Get code](assets/img_033.png)

### Como integrar ao maia-demo.html

1. No **CX Agent Studio → Deploy → widget-maia-demo → ⋮ → Get code**, copie os dois blocos de código
2. **Bloco 1** — cole dentro do `<head>` do arquivo `maia-demo.html`:
   ```html
   <script defer src="..."></script>
   <link rel="..." />
   <link rel="..." />
   ```
3. **Bloco 2** — substitua o trecho `COLE_SEU_DEPLOYMENT_NAME_AQUI` pelo `<script>` com o `deploymentName`
4. Salve e abra `maia-demo.html` no navegador

---

## Resultado Final

O agente Maia está pronto com:

- Root agent com personalidade e regras de negócio
- Sub-agente FAQ especializado
- Delegação automática entre agentes
- Respostas em português
- Rastreabilidade com Steps e JSON
- Widget deployado e integrado ao HTML
