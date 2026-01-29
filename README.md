# Automation
Automation N8N 

📊 Relatório Mensal de Ocorrências — Automação n8n + Slack
📌 Visão Geral

Este projeto consiste em um workflow no n8n que automatiza a geração e o envio de Relatórios Mensais de Ocorrências por TL, a partir de uma planilha.

O fluxo:

Lê linhas de ocorrências (planilha)

Agrupa e consolida os dados por TL

Gera um texto de relatório automático

Envia o relatório por DM no Slack para cada TL

Cada TL recebe apenas o seu próprio relatório, de forma automática.

🎯 Objetivo

Eliminar a criação manual de relatórios mensais, automatizando:

Total de falhas por TL

Distribuição de falhas por tipo

Top 3 reps (LDAP) com mais ocorrências

Geração de texto pronto para envio

Entrega automática no Slack

🗂 Estrutura dos Dados (Input)

Cada linha da planilha representa uma ocorrência.

Colunas esperadas:

TL

OCORRENCIA ou OCORRENCIA

LDAP/Usuário ou LDAP

Mês ou Mes

Observações:

Algumas colunas podem vir “sujas” (aspas extras, espaços, textos adicionais).

O workflow já trata normalização de TL, remoção de aspas ("), campos vazios e variações de nomes de colunas.

⚙️ Arquitetura do Workflow
🔹 1. Entrada de dados

Fonte: planilha (ex: Google Sheets, Excel, CSV, etc.)

Cada item = 1 ocorrência.

🔹 2. Verdicode 1 — Agregador por TL

Responsável por transformar várias linhas em 1 item por TL, agregando:

total_tl → total de ocorrências

tipos_counter → contagem por tipo

reps → contagem por LDAP

mes_atual → primeiro mês válido encontrado

Principais tratamentos:

Limpeza de aspas

Normalização de TL (split em vírgula, trim)

Fallback de chaves (OCORRENCIA vs OCORRENCIA )

Normalização de reps (lowercase)

Saída:
👉 1 item = 1 TL consolidado

🔹 3. Verdicode 2 — Montagem do relatório

Cria o campo:

relatorio_texto


Contendo:

Título com mês

TL responsável

Total de falhas

Distribuição ordenada

Top 3 reps com mais ocorrências

O texto já sai formatado para envio direto no Slack.

🔹 4. Mapeamento TL → Slack ID

Node de código que:

Normaliza o nome do TL

Busca no dicionário (MAP)

Adiciona no item:

slack_user_id


Se um TL não existir no mapa, ele é registrado para log/validação.

🔹 5. Envio no Slack (DM)

Node “Send Message” do Slack:

Channel

{{ $json.slack_user_id }}


Text

{{ $json.relatorio_texto }}


Resultado:
👉 Cada TL recebe automaticamente sua DM com o relatório mensal.

🚫 Por que não existe Slash Command / Bot?

Foi tentado criar um Slack App com Slash Command, mas não foi possível concluir porque:

O n8n está atrás de login corporativo (SSO/cookie)

Slack exige endpoint HTTPS público sem autenticação

Slack não consegue consumir webhooks protegidos por sessão

Conclusão:

❌ Bot interativo inviável pela infra atual

✅ Envio automático por DM funciona perfeitamente

🧠 Resumo do Projeto

Workflow no n8n que lê uma planilha de ocorrências, agrupa os dados por TL, gera automaticamente relatórios mensais com métricas e envia cada relatório por DM no Slack para o respectivo TL.

📈 Possíveis Evoluções

Dashboard mensal integrado (Looker / Sheets)

Armazenar histórico por mês

Ranking geral automático

Detecção de picos de falha

Endpoint público para permitir bot/slash command