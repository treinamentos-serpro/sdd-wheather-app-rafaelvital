# Especificação de Produto — Weather App

## Overview

O Weather App é uma aplicação responsiva para consultas ocasionais de condições meteorológicas por pessoas do público geral. No MVP, a pessoa pesquisa manualmente uma cidade no Brasil, seleciona um resultado e consulta o clima atual e a previsão diária de hoje e dos quatro dias seguintes, de acordo com o calendário local da cidade.

A fonte meteorológica definida é o Open-Meteo. A interface e a formatação regional são em português brasileiro. O produto atende ao objetivo qualitativo de permitir consultar diferentes cidades e ver seu clima; não há KPI de negócio definido para o MVP. A última cidade consultada e a unidade de temperatura são lembradas localmente, sem conta ou sincronização entre dispositivos.

## Functional Requirements

- **FR-01 — Buscar cidades:** permitir pesquisa manual pelo nome de uma cidade brasileira, iniciada por Enter ou pelo botão de busca, a partir de dois caracteres. Não pesquisar automaticamente durante a digitação nem solicitar localização do dispositivo.
- **FR-02 — Exibir e selecionar resultados:** apresentar até dez resultados ordenados por relevância, com cidade, estado/região e país quando fornecidos pelo provedor. Permitir selecionar um resultado. Se faltarem dados de localização, mostrar somente os dados disponíveis, sem inventá-los. Quando não houver resultados, orientar a pessoa a refinar a consulta.
- **FR-03 — Consultar clima atual:** para a cidade selecionada, exibir temperatura, condição meteorológica em texto e ícone, sensação térmica, umidade, vento e precipitação. Identificar a cidade e o horário da observação informado pela fonte.
- **FR-04 — Consultar previsão diária:** exibir hoje e os quatro dias seguintes conforme as datas do calendário local da cidade selecionada. Para cada dia, exibir temperatura mínima e máxima, condição em texto e ícone, probabilidade e/ou volume de chuva disponíveis, e vento. O primeiro dia pode ser parcial.
- **FR-05 — Alternar unidade de temperatura:** permitir alternar entre Celsius e Fahrenheit e aplicar a unidade escolhida a todas as temperaturas apresentadas. Celsius é o padrão inicial; vento é apresentado em km/h e precipitação em mm.
- **FR-06 — Lembrar preferências:** lembrar localmente a última cidade selecionada e a unidade de temperatura e restaurá-las quando a aplicação for aberta novamente no mesmo dispositivo e navegador. Não exigir conta nem sincronizar dados entre dispositivos.
- **FR-07 — Atualizar dados:** atualizar automaticamente os dados a cada 30 minutos e oferecer uma ação de atualização manual. Distinguir horário de observação do clima atual, horário de obtenção bem-sucedida da previsão e horário da última tentativa de atualização.
- **FR-08 — Comunicar estados e falhas:** apresentar estados explícitos de carregamento, vazio, indisponibilidade e erro. Em falha parcial, manter disponível cada seção carregada e identificar separadamente a seção que falhou, com opção de tentar novamente. Dados vencidos podem continuar visíveis com aviso explícito e horário, mas nunca podem ser apresentados como atuais.
- **FR-09 — Usar em dispositivos móveis:** permitir concluir em dispositivo móvel os fluxos de busca, seleção, consulta do clima atual e leitura da previsão diária, sem perda de funcionalidade em relação ao uso desktop.
- **FR-10 — Identificar fonte dos dados:** disponibilizar atribuição ao Open-Meteo na seção Sobre/Créditos.

## User Stories

- **US-01:** Como pessoa que quer verificar o tempo em uma cidade, quero pesquisar seu nome e escolher o local correto para consultar dados relevantes para aquele lugar.
- **US-02:** Como pessoa que consulta o tempo, quero ver as condições atuais e quando foram observadas para entender o que está acontecendo na cidade selecionada.
- **US-03:** Como pessoa que planeja os próximos dias, quero consultar a previsão diária de hoje e dos quatro dias seguintes no horário local da cidade.
- **US-04:** Como pessoa que prefere outra unidade, quero alternar entre Celsius e Fahrenheit para interpretar as temperaturas com facilidade.
- **US-05:** Como pessoa que retorna à aplicação, quero reencontrar minha última cidade e unidade neste dispositivo para evitar repetir minhas escolhas.
- **US-06:** Como pessoa que consulta dados meteorológicos, quero saber quando eles estão sendo atualizados ou quando uma consulta falha para não confundir informação antiga com atual.
- **US-07:** Como pessoa que usa um dispositivo móvel, quero buscar uma cidade e ler o clima e a previsão sem perder informações ou controles.
- **US-08:** Como pessoa que deseja conhecer a origem dos dados, quero encontrar a atribuição do provedor meteorológico.

## Acceptance Criteria

Os critérios abaixo são verificáveis e referenciam os requisitos funcionais correspondentes.

- **AC-01 (FR-01):** Com menos de dois caracteres, a busca não pode ser enviada. Com dois ou mais caracteres, enviar por Enter ou pelo botão inicia a consulta; digitar sem enviar não inicia uma consulta. A aplicação não solicita permissão nem dados de localização do dispositivo.
- **AC-02 (FR-02):** Uma busca com resultados apresenta no máximo dez itens em ordem de relevância e permite selecionar um. Cada item mostra os dados de cidade, estado/região e país retornados pela fonte, sem preencher campos ausentes com valores inventados. Uma busca sem resultados apresenta orientação para refinar a consulta.
- **AC-03 (FR-03):** Após selecionar uma cidade, a seção de clima atual identifica a cidade e apresenta temperatura, condição textual e ícone, sensação térmica, umidade, vento, precipitação e horário de observação quando esses dados estiverem disponíveis na fonte. Se uma consulta necessária falhar, a interface identifica a falha sem rotular dados vencidos como atuais.
- **AC-04 (FR-04):** A previsão contém exatamente as datas locais de hoje e dos quatro dias seguintes para a cidade selecionada. Cada dia apresenta os campos previstos disponíveis: mínima e máxima, condição textual e ícone, probabilidade e/ou volume de chuva, e vento. A data exibida não muda por causa do fuso do dispositivo que consulta; o primeiro dia pode representar um período parcial.
- **AC-05 (FR-05):** Na primeira utilização, as temperaturas são apresentadas em Celsius. Depois de alternar a unidade, todas as temperaturas visíveis passam a usar Fahrenheit ou Celsius, conforme selecionado; vento permanece em km/h e precipitação em mm.
- **AC-06 (FR-06):** Após selecionar cidade e unidade, fechar e reabrir a aplicação no mesmo dispositivo e navegador restaura essas escolhas. Em armazenamento local indisponível ou sem escolhas anteriores, a aplicação continua utilizável e usa Celsius como unidade inicial.
- **AC-07 (FR-07):** Com a aplicação aberta, os dados têm nova tentativa automática a cada 30 minutos e podem ser atualizados por uma ação manual. A interface distingue horário de observação, obtenção bem-sucedida da previsão e última tentativa. O clima atual é considerado válido por até uma hora desde a observação; a previsão, por até seis horas desde a obtenção bem-sucedida.
- **AC-08 (FR-08):** Durante carregamento, indisponibilidade, ausência de resultados ou falha, a interface comunica o estado correspondente. Se apenas uma seção falhar, outra seção carregada permanece acessível. A falha oferece nova tentativa. Quando dados vencidos forem mantidos visíveis, há aviso explícito e horário; eles não são identificados como atuais.
- **AC-09 (FR-09):** Nos tamanhos, orientações e condições de zoom especificados em NFR-01, uma pessoa consegue pesquisar, selecionar uma cidade, consultar o clima atual e visualizar os cinco dias da previsão, sem rolagem horizontal nem controles ou conteúdo essenciais inacessíveis.
- **AC-10 (FR-10):** A atribuição ao Open-Meteo pode ser encontrada na seção Sobre/Créditos.

## Non-Functional Requirements

- **NFR-01 — Responsividade:** entre 320 px e 1440 px, adaptar conteúdo e controles sem rolagem horizontal nem perda de funcionalidades. Validar em 320, 375, 768, 1024 e 1440 px, em retrato e paisagem, incluindo zoom de 200%.
- **NFR-02 — Usabilidade:** rótulos e estados de busca, seleção de cidade e unidade devem ser compreensíveis. No MVP, a equipe fará uma validação interna por roteiro, sem participantes externos. Registrar participantes, tarefas, conclusão de cada tarefa sem ajuda e problemas encontrados; não apresentar o resultado como evidência de usabilidade com o público geral.
- **NFR-03 — Clareza e localização:** cada conjunto de clima atual e previsão identifica claramente cidade e período. Datas e números seguem padrões pt-BR; horários usam relógio de 24 horas e identificam o fuso local da cidade consultada.
- **NFR-04 — Atualidade e consistência:** o clima atual é válido por até uma hora desde a observação indicada pela fonte. A previsão é válida por até seis horas desde a obtenção bem-sucedida pelo app. Se os dados vencerem, podem permanecer visíveis somente com aviso explícito e horário. Uma falha parcial afeta apenas a seção correspondente. O horário da última tentativa não substitui nem altera os horários de observação/obtenção.
- **NFR-05 — Desempenho de busca:** em uma janela de sete dias, o percentil 95 do tempo ponta a ponta de busca deve ser de até dois segundos, incluindo o provedor; medir desde o envio da busca até os resultados ficarem visíveis no navegador. O protocolo registra modelo do dispositivo, sistema operacional, versão do navegador, região, rede, quantidade de amostras e política de cache. Usar celular de faixa intermediária e conexão 4G no Brasil.
- **NFR-06 — Acessibilidade:** atender ao nível AA da WCAG 2.2. A evidência inclui auditoria automática e testes manuais de teclado, foco e leitor de tela. Corrigir violações aplicáveis identificadas.
- **NFR-07 — Disponibilidade:** 99,5% mensal é objetivo futuro, não uma meta mensurável para o MVP. Durante falhas externas, cumprir FR-08 e AC-08.
- **NFR-08 — Compatibilidade:** suportar versões estável atual e imediatamente anterior de Chrome, Edge, Firefox e Safari em sistemas operacionais desktop e móveis. A matriz exata de sistemas operacionais e versões cobertas deve ser registrada antes da validação de compatibilidade (ver Open Questions).
- **NFR-09 — Fonte e uso dos dados:** antes do lançamento, validar cobertura no Brasil, campos necessários, frequência de atualização, limites e condições de uso do Open-Meteo. A atribuição deve estar disponível em Sobre/Créditos. O cache local não deve estender as validades de FR-07/NFR-04.

## Edge Cases

- Consulta vazia, com menos de dois caracteres, sem resultados ou com mais de dez correspondências.
- Cidades homônimas, nomes com acentos ou caracteres especiais, ou resultados sem estado/região ou país fornecidos pela fonte.
- Cidade pretendida sem cobertura ou sem coordenadas/dados necessários no provedor.
- Falha, demora excessiva, resposta inválida, limite de consultas ou indisponibilidade do provedor durante busca, clima atual ou previsão.
- Falha parcial: clima atual disponível e previsão indisponível, ou o contrário.
- Dados atuais vencidos há mais de uma hora e previsão vencida há mais de seis horas, inclusive quando a atualização mais recente falha.
- Mudança de dia no fuso local da cidade, inclusive quando o fuso local difere do fuso do dispositivo; primeiro dia da previsão parcial.
- Campos meteorológicos opcionais ausentes na resposta da fonte; não inventar valores nem apresentar um campo ausente como zero.
- Alternância de unidade durante carregamento, erro ou reabertura da aplicação.
- Armazenamento local bloqueado, indisponível ou sem dados válidos; a consulta deve continuar funcional sem persistência.
- Atualização manual repetida ou coincidente com uma atualização automática; a interface deve manter estados compreensíveis e não misturar horários de tentativas e de dados.
- Orientação retrato/paisagem, larguras de tela especificadas e zoom de 200%, sem conteúdo ou controles essenciais cortados ou sobrepostos.

## Assumptions

- A pessoa consulta uma cidade por vez e informa sua escolha por busca manual; a aplicação não pede localização do dispositivo.
- O Open-Meteo oferece, nas localidades atendidas, dados suficientes para os campos e períodos requeridos. Essa premissa deve ser validada antes do lançamento; campos não fornecidos não serão inventados.
- A última cidade e a unidade são armazenadas no dispositivo/navegador local, sem conta, sincronização ou promessa de persistência entre dispositivos ou navegadores.
- Celsius é a unidade padrão inicial; a escolha posterior é lembrada localmente. Todas as temperaturas obedecem à unidade selecionada, vento é exibido em km/h e precipitação em mm.
- A interface é em pt-BR; datas e horas da previsão e do clima seguem o calendário e o fuso da cidade selecionada.
- A atualização automática a cada 30 minutos ocorre enquanto a aplicação está aberta. O comportamento em segundo plano ou com a aplicação fechada não é requisito do MVP.
- O primeiro dia da previsão pode ser parcial, pois a previsão começa no dia local da consulta.
- O resultado da validação interna de usabilidade se aplica somente ao roteiro e aos participantes registrados, não ao público geral.

## Risks

| Risco | Probabilidade / impacto | Mitigação |
|---|---|---|
| Indisponibilidade, lentidão ou limites do Open-Meteo impedirem consultas ou degradarem a experiência. | Média / Alto | Validar cobertura, limites, atualização e condições de uso antes do lançamento; tratar falhas e permitir nova tentativa; seguir FR-08 sem apresentar dados vencidos como atuais. |
| Dados desatualizados ou períodos interpretados com fuso incorreto levarem a informação enganosa ou à data errada. | Média / Alto | Aplicar as validades de NFR-04, identificar horários e fuso local, e validar mudanças de data em diferentes fusos. |
| Cidades homônimas ou cobertura insuficiente levarem à seleção do local errado. | Média / Alto | Exibir os dados de localização disponibilizados para desambiguação, ordenar por relevância, limitar resultados e não inventar campos ausentes. |
| A busca não atingir P95 de dois segundos, especialmente em rede móvel. | Média / Médio/alto | Medir ponta a ponta pelo protocolo de NFR-05, incluindo o provedor, e registrar as condições de ensaio. |
| Dispositivos, navegadores, orientações ou tecnologias assistivas não contemplados apresentarem barreiras. | Média / Alto | Fechar e registrar a matriz de compatibilidade; executar validações responsivas e de WCAG 2.2 AA definidas nesta spec. |
| Não haver KPI de produto, medição de disponibilidade no MVP ou teste externo de usabilidade. | Média / Médio/alto | Tratar sucesso do MVP como objetivo qualitativo; não declarar evidência não coletada; planejar métricas, monitoramento ou pesquisa externa caso sejam exigidos para lançamento. |
| Requisitos de atribuição, limites ou condições de uso do provedor inviabilizarem a experiência planejada. | Média / Alto | Confirmar termos, limites, cobertura e atribuição antes do lançamento. |

As probabilidades são estimativas qualitativas do discovery e devem ser revistas após as validações do provedor e os testes do produto.

## Out of Scope

- Localização automática, solicitação de permissão de localização e detecção automática do local.
- Favoritos, múltiplas cidades simultâneas, contas e sincronização entre dispositivos.
- Previsão horária.
- Alertas meteorológicos, avisos de tempo severo e recomendações.
- Idiomas além de pt-BR.
- Meta mensurável de disponibilidade mensal no MVP; 99,5% permanece objetivo futuro.
- Testes de usabilidade com participantes externos no MVP.

## Open Questions

1. **Matriz de compatibilidade:** quais sistemas operacionais e versões exatas serão considerados desktop e móveis para Chrome, Edge, Firefox e Safari? A seleção deve ser registrada antes dos testes de compatibilidade.
2. **Protocolo de desempenho:** qual modelo de celular de faixa intermediária, versão de sistema operacional/navegador, região, tamanho de amostra e política de cache serão usados? O protocolo deve registrar todos os campos exigidos por NFR-05 antes da medição.
3. **Roteiro de usabilidade interna:** quem serão os participantes da equipe e qual roteiro final de tarefas será usado? Registrar participantes, tarefas, conclusão sem ajuda e problemas observados, sem extrapolar os resultados para usuários externos.
4. **Cobertura e campos do provedor:** o Open-Meteo atende às cidades brasileiras pretendidas e fornece os campos atuais e diários necessários, inclusive probabilidade/volume de chuva e fuso local? Confirmar cobertura, frequência, limites e condições de uso antes do lançamento.
5. **Precisão de apresentação:** qual regra de arredondamento será usada para temperaturas, umidade, precipitação e vento? Definir antes da implementação para manter consistência entre unidades e previsões.
6. **Atualização em segundo plano:** é necessário atualizar dados quando a aplicação está em segundo plano ou fechada? Isso não faz parte do MVP conforme a premissa atual.
7. **Objetivos futuros:** quais indicadores quantitativos de sucesso do produto e de disponibilidade devem ser definidos após o MVP, se forem necessários para avaliar lançamento e operação?
