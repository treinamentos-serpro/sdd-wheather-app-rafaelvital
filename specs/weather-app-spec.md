# Especificação de Produto — Weather App

## Overview

O Weather App é uma aplicação responsiva para consultas ocasionais de condições meteorológicas por pessoas do público geral. No MVP, a pessoa pesquisa manualmente uma cidade no Brasil, seleciona um resultado e consulta o clima atual e a previsão diária de hoje e dos quatro dias seguintes, de acordo com o calendário local da cidade.

A fonte meteorológica definida é o Open-Meteo. A interface e a formatação regional são em português brasileiro. O produto atende ao objetivo qualitativo de permitir consultar diferentes cidades e ver seu clima; não há KPI de negócio definido para o MVP. A última cidade consultada e a unidade de temperatura são lembradas localmente, sem conta ou sincronização entre dispositivos.

## Functional Requirements

- **FR-01 — Buscar cidades:** permitir pesquisa manual pelo nome de uma cidade brasileira, iniciada por Enter ou pelo botão de busca, com no mínimo dois e no máximo 100 pontos de código Unicode após remover espaços em branco das extremidades. Enviar a consulta com filtro de país Brasil e não apresentar resultados de outros países. Não pesquisar automaticamente durante a digitação nem solicitar localização do dispositivo. Se buscas forem enviadas em sequência, somente a resposta da busca mais recente pode atualizar a lista.
- **FR-02 — Exibir e selecionar resultados:** apresentar até dez resultados brasileiros ordenados por relevância, com cidade, estado/região e país quando fornecidos pelo provedor. Permitir selecionar um resultado. Se faltarem dados de localização, mostrar somente os dados disponíveis, sem inventá-los. Quando não houver resultados, orientar a pessoa a refinar a consulta.
- **FR-03 — Consultar clima atual:** para a cidade selecionada, exibir temperatura e horário válido da observação como dados essenciais; exibir condição meteorológica em texto e ícone, sensação térmica, umidade, vento e precipitação quando fornecidos e válidos. Identificar claramente a cidade. Se faltar temperatura ou horário válido, a seção é incompleta/indisponível, não atual.
- **FR-04 — Consultar previsão diária:** exibir hoje e os quatro dias seguintes conforme as datas do calendário local da cidade selecionada. Para cada dia, data, mínima e máxima válidas são dados essenciais; condição textual e ícone, probabilidade de precipitação, volume diário de precipitação e vento máximo são exibidos quando fornecidos e válidos. O primeiro dia pode ser parcial.
- **FR-05 — Alternar unidade de temperatura:** permitir alternar entre Celsius e Fahrenheit e aplicar a unidade escolhida a todas as temperaturas apresentadas. Celsius é o padrão inicial; vento é apresentado em km/h e precipitação em mm.
- **FR-06 — Lembrar preferências:** lembrar localmente a última cidade selecionada e a unidade de temperatura e restaurá-las quando a aplicação for aberta novamente no mesmo dispositivo e navegador. Ao restaurar uma cidade, iniciar uma nova consulta; não tratar dados meteorológicos de uma sessão anterior como atuais. Sem armazenamento local, manter a aplicação utilizável sem persistência. Não exigir conta nem sincronizar dados entre dispositivos.
- **FR-07 — Atualizar dados:** atualizar cada seção automaticamente a cada 30 minutos enquanto a página estiver visível e oferecer uma ação manual para atualizar as seções elegíveis. Contar o intervalo de cada seção a partir do fim de sua última tentativa; não iniciar chamadas simultâneas para a mesma seção e cidade. Ao voltar à página após ela ficar oculta, atualizar imediatamente as seções cujo intervalo venceu. Distinguir horário de observação do clima atual, horário de obtenção bem-sucedida da previsão e horário da última tentativa de atualização.
- **FR-08 — Comunicar estados e falhas:** apresentar estados explícitos de carregamento, vazio, indisponibilidade, erro e dados incompletos. Em falha parcial, manter disponível cada seção carregada e identificar separadamente a seção que falhou, com opção de tentar novamente. Dados vencidos podem continuar visíveis com aviso explícito e horário, mas nunca podem ser apresentados como atuais. Dados meteorológicos de uma cidade anterior não podem ser identificados como pertencentes à cidade recém-selecionada.
- **FR-09 — Usar em dispositivos móveis:** permitir concluir em dispositivo móvel os fluxos de busca, seleção, consulta do clima atual e leitura da previsão diária, sem perda de funcionalidade em relação ao uso desktop.
- **FR-10 — Identificar fonte dos dados:** disponibilizar na seção Sobre/Créditos a atribuição textual “Dados meteorológicos: Open-Meteo”, com link para https://open-meteo.com/.

## Contrato de dados e apresentação

- **Geocodificação:** consultar `https://geocoding-api.open-meteo.com/v1/search` com o texto aparado, `countryCode=BR`, `count=10` e `language=pt`. Preservar os caracteres da consulta. Exibir os resultados na ordem retornada, sem completar campos ausentes. Um resultado só pode iniciar consultas meteorológicas se tiver nome e latitude entre −90 e 90 e longitude entre −180 e 180; resultados sem esses dados podem ser mostrados, mas não selecionados, com indicação de indisponibilidade.
- **Meteorologia:** para cada cidade selecionada, fazer consultas independentes ao endpoint `https://api.open-meteo.com/v1/forecast`: uma com `current` e outra com `daily`, para que falhas de rede e novas tentativas sejam independentes por seção. Usar coordenadas válidas do resultado selecionado, `timezone=auto`, `forecast_days=5`, `temperature_unit=celsius`, `wind_speed_unit=kmh` e `precipitation_unit=mm`. Solicitar `temperature_2m`, `apparent_temperature`, `relative_humidity_2m`, `precipitation`, `weather_code` e `wind_speed_10m` para clima atual; para a previsão, `temperature_2m_min`, `temperature_2m_max`, `weather_code`, `precipitation_probability_max`, `precipitation_sum` e `wind_speed_10m_max`, além das datas diárias. Horário e fuso vêm dos campos temporais e metadados da resposta.
- **Integridade:** aceitar somente números finitos e datas/horários parseáveis. Temperatura e sensação térmica podem ser negativas; umidade e probabilidade de precipitação devem estar entre 0 e 100%; precipitação e vento não podem ser negativos; mínima não pode exceder máxima. Valor complementar inválido é omitido. Campo essencial inválido torna incompleta a seção ou o dia afetado. Não substituir valores inválidos por zero.
- **Condição e ícone:** derivar ambos do código meteorológico válido retornado pela fonte, usando o mapeamento de códigos WMO disponibilizado pelo Open-Meteo. Código desconhecido ou ausente não impede a exibição dos demais dados e não deve gerar condição ou ícone inventados.
- **Unidades e arredondamento:** manter os valores recebidos em Celsius como base. Exibir temperaturas em graus inteiros; para Fahrenheit, converter primeiro (`°F = °C × 9/5 + 32`) e então arredondar ao inteiro mais próximo. Exibir umidade e probabilidade de precipitação como percentuais inteiros, precipitação em mm com uma casa decimal e vento em km/h como inteiro. Usar as mesmas regras no clima atual e na previsão.
- **Datas e horas:** usar o fuso IANA retornado pelo provedor para a cidade e as datas locais da previsão, nunca o fuso do dispositivo. Exibir horários no formato de 24 horas, com o fuso IANA e o deslocamento UTC aplicável naquele instante. A hora de obtenção da previsão e a hora de conclusão da última tentativa (sucesso, erro ou timeout) são registradas pelo app em UTC e apresentadas localmente com fuso identificável.
- **Validade:** considerar o clima atual válido até 60 minutos, inclusive, após o horário de observação; considerar a previsão válida até seis horas, inclusive, após a obtenção bem-sucedida pelo app. Dado inválido, ausente, futuro em relação ao relógio do app ou fora do prazo não pode ser rotulado como atual/válido. A previsão usa como referência o instante em que a resposta válida foi recebida e processada, não o início da requisição nem a última tentativa.
- **Concorrência:** atualizar clima atual e previsão de forma independente. Uma nova tentativa para uma seção não pode iniciar outra requisição simultânea para a mesma seção e cidade; a ação manual atualiza ambas as seções elegíveis. Respostas de uma cidade ou busca anterior não podem sobrescrever dados da seleção mais recente.

## User Stories

- **US-01:** Como usuário comum, quero pesquisar uma cidade e selecionar o local correto para consultar dados meteorológicos daquele lugar. *(FR-01, FR-02)*
- **US-02:** Como usuário comum, quero ver o clima atual e o horário da observação para entender as condições recentes na cidade selecionada. *(FR-03)*
- **US-03:** Como usuário comum, quero ver a previsão diária de hoje e dos quatro dias seguintes no calendário local da cidade para me planejar. *(FR-04)*
- **US-04:** Como usuário comum, quero alternar entre Celsius e Fahrenheit para interpretar as temperaturas na unidade que prefiro. *(FR-05)*
- **US-05:** Como usuário comum, quero que minha última cidade e unidade sejam lembradas neste dispositivo para retomar uma consulta sem repetir minhas escolhas. *(FR-06)*
- **US-06:** Como usuário comum, quero atualizar os dados e receber avisos claros quando houver falha ou informação vencida para não confundir dados antigos com atuais. *(FR-07, FR-08)*
- **US-07:** Como usuário comum, quero buscar uma cidade e consultar o clima atual e a previsão pelo celular para obter as mesmas informações fora do desktop. *(FR-09)*
- **US-08:** Como usuário comum, quero encontrar a atribuição da fonte meteorológica para saber de onde vêm os dados. *(FR-10)*

## Acceptance Criteria

Cada critério usa um cenário Given/When/Then com resultado observável e referência ao requisito funcional correspondente.

### AC-01 — Buscar cidades (FR-01)

**Cenário 1: impedir envio de consulta curta**
- **Given:** o campo de busca contém menos de dois caracteres após remover espaços nas extremidades.
- **When:** o usuário tenta enviar a busca por Enter ou pelo botão.
- **Then:** nenhuma consulta de geocodificação é enviada.

**Cenário 2: enviar busca por Enter ou botão**
- **Given:** o campo contém pelo menos dois caracteres após remover espaços nas extremidades.
- **When:** o usuário envia a busca por Enter ou ativa o botão.
- **Then:** uma consulta é enviada com o texto aparado e o filtro de país Brasil.

**Cenário 3: não buscar durante a digitação nem solicitar localização**
- **Given:** o campo de busca está vazio.
- **When:** o usuário digita um texto sem enviá-lo.
- **Then:** nenhuma consulta é enviada e nenhuma solicitação de permissão de localização é apresentada.

**Cenário 4: não enviar entrada vazia ou composta somente por espaços**
- **Given:** o campo está vazio ou contém somente espaços.
- **When:** o usuário tenta enviar a busca por Enter ou pelo botão.
- **Then:** nenhuma consulta de geocodificação é enviada e a interface orienta a informar uma cidade; a cidade selecionada não é alterada.

**Cenário 5: preservar caracteres do nome**
- **Given:** o campo contém pelo menos dois caracteres e inclui acentos, hífen ou apóstrofo.
- **When:** o usuário envia a busca.
- **Then:** a consulta mantém esses caracteres e os resultados apresentam os nomes retornados pelo provedor sem corrupção de codificação.

**Cenário 6: rejeitar consulta acima do limite**
- **Given:** o campo contém mais de 100 pontos de código Unicode após remover espaços em branco das extremidades.
- **When:** o usuário tenta enviar a busca.
- **Then:** nenhuma consulta é enviada e a interface informa o limite permitido.

**Cenário 7: descartar resposta de busca antiga**
- **Given:** uma busca é enviada e, antes de sua resposta chegar, o usuário envia uma busca diferente.
- **When:** as respostas chegam fora de ordem.
- **Then:** somente os resultados da busca mais recente são exibidos e selecionáveis.

### AC-02 — Exibir e selecionar resultados (FR-02)

**Cenário 1: limitar e ordenar resultados**
- **Given:** o provedor retorna mais de dez resultados ordenados por relevância.
- **When:** os resultados da busca são exibidos.
- **Then:** a lista contém no máximo os dez primeiros resultados, na mesma ordem de relevância retornada.

**Cenário 2: mostrar contexto de localização disponível**
- **Given:** um resultado contém cidade, estado/região e país.
- **When:** a lista de resultados é exibida.
- **Then:** os três valores aparecem associados ao resultado.

**Cenário 3: não inventar dados ausentes**
- **Given:** um resultado não contém estado/região ou país.
- **When:** a lista de resultados é exibida.
- **Then:** os campos ausentes não são preenchidos com valores não fornecidos pelo provedor.

**Cenário 4: selecionar um resultado**
- **Given:** há pelo menos um resultado de busca visível.
- **When:** o usuário seleciona um resultado.
- **Then:** esse local passa a ser a cidade selecionada para as consultas meteorológicas.

**Cenário 5: informar busca sem resultados**
- **Given:** o provedor retorna uma lista vazia para a consulta.
- **When:** a busca termina.
- **Then:** a lista de resultados permanece vazia e a interface orienta o usuário a refinar a consulta.

**Cenário 6: não exibir resultados fora do Brasil**
- **Given:** a resposta do provedor contém resultados de outros países.
- **When:** os resultados da busca são exibidos.
- **Then:** nenhum resultado de fora do Brasil é apresentado ou pode ser selecionado.

**Cenário 7: comunicar falha na busca**
- **Given:** a consulta de geocodificação retorna erro, resposta inválida ou excede o timeout definido em NFR-10.
- **When:** a busca termina.
- **Then:** a interface identifica erro ou indisponibilidade, oferece nova tentativa e não altera a cidade selecionada nem associa a ela dados de outra busca.

**Cenário 8: isolar dados ao trocar a cidade**
- **Given:** há dados carregados para uma cidade e o usuário seleciona outra.
- **When:** as consultas para a nova cidade estão carregando ou falham.
- **Then:** nenhum dado da cidade anterior é apresentado como pertencente à nova; se os dados anteriores permanecerem visíveis, continuam identificados com a cidade anterior.

### AC-03 — Consultar clima atual (FR-03)

**Cenário 1: exibir os dados essenciais e complementares disponíveis**
- **Given:** uma cidade está selecionada e o provedor retorna temperatura, horário de observação válido e quaisquer campos complementares.
- **When:** a consulta de clima atual termina com sucesso.
- **Then:** a seção identifica a cidade e exibe temperatura e horário de observação; exibe também cada campo complementar válido que a fonte forneceu.

**Cenário 2: tratar dado essencial ausente ou inválido**
- **Given:** uma cidade está selecionada e a resposta não contém temperatura válida ou horário de observação válido.
- **When:** a consulta de clima atual termina com sucesso técnico.
- **Then:** a seção é identificada como incompleta ou indisponível e não apresenta os dados como clima atual.

**Cenário 3: omitir campos complementares ausentes**
- **Given:** a resposta não contém um ou mais campos complementares de clima atual.
- **When:** a consulta termina com temperatura e horário de observação válidos.
- **Then:** os campos complementares disponíveis são exibidos e os ausentes não são apresentados como zero nem recebem valores inventados.

### AC-04 — Consultar previsão diária (FR-04)

**Cenário 1: exibir cinco datas no calendário local da cidade**
- **Given:** a cidade selecionada tem um fuso horário diferente do dispositivo e a fonte retorna a previsão diária.
- **When:** a previsão é exibida.
- **Then:** são exibidas as datas locais de hoje e dos quatro dias seguintes, totalizando cinco datas consecutivas, independentemente do fuso do dispositivo.

**Cenário 2: exibir os dados diários essenciais e complementares**
- **Given:** a fonte retorna data local, mínima e máxima válidas e quaisquer campos complementares.
- **When:** a previsão é exibida.
- **Then:** cada dia mostra data, mínima e máxima e os campos complementares válidos fornecidos: condição textual e ícone, probabilidade de precipitação, volume diário de precipitação e vento máximo.

**Cenário 3: tratar dados essenciais diários ausentes**
- **Given:** a resposta diária não contém data local válida ou mínima/máxima válidas para um dia.
- **When:** a previsão é exibida.
- **Then:** esse dia é identificado como incompleto/indisponível e não recebe valores inventados; os outros dias válidos continuam visíveis.

**Cenário 4: omitir campos complementares diários ausentes**
- **Given:** um ou mais campos complementares não foram fornecidos para um dia com data e temperaturas válidas.
- **When:** a previsão é exibida.
- **Then:** os campos disponíveis são mostrados e os ausentes não são substituídos por zero ou por valores não fornecidos.

**Cenário 5: rejeitar valores inconsistentes**
- **Given:** uma resposta contém valor não numérico, unidade inesperada, umidade/probabilidade fora de 0–100%, precipitação/vento negativo ou mínima maior que máxima.
- **When:** os dados são validados.
- **Then:** o valor complementar inválido é omitido ou o dado essencial afetado é marcado como incompleto; nenhuma inconsistência é apresentada como válida.

### AC-05 — Alternar unidade de temperatura (FR-05)

**Cenário 1: usar Celsius por padrão**
- **Given:** não há unidade previamente salva.
- **When:** os dados meteorológicos são exibidos pela primeira vez.
- **Then:** todas as temperaturas são apresentadas em Celsius.

**Cenário 2: aplicar unidade selecionada a todas as temperaturas**
- **Given:** clima atual e previsão estão visíveis.
- **When:** o usuário alterna de Celsius para Fahrenheit ou de Fahrenheit para Celsius.
- **Then:** todas as temperaturas visíveis passam a usar a unidade escolhida, enquanto vento permanece em km/h e precipitação em mm.

### AC-06 — Lembrar preferências (FR-06)

**Cenário 1: restaurar cidade e unidade salvas**
- **Given:** o usuário selecionou uma cidade e uma unidade e os dados foram salvos localmente.
- **When:** a aplicação é fechada e reaberta no mesmo dispositivo e navegador.
- **Then:** a última cidade e a unidade selecionada são restauradas e uma nova consulta para essa cidade é iniciada.

**Cenário 2: continuar sem armazenamento local**
- **Given:** o armazenamento local está indisponível, contém dados inválidos ou não há preferências salvas.
- **When:** a aplicação é aberta.
- **Then:** a aplicação permanece utilizável sem persistência, usa Celsius se não houver unidade válida e não restaura uma cidade inválida.

### AC-07 — Atualizar dados (FR-07)

**Cenário 1: atualizar automaticamente a cada 30 minutos**
- **Given:** a página está visível, uma cidade está selecionada e terminou uma tentativa de atualização.
- **When:** passam 30 minutos desde o fim dessa tentativa.
- **Then:** uma nova tentativa de atualização é iniciada.

**Cenário 2: atualizar ao retornar à página**
- **Given:** a página ficou oculta e o intervalo de 30 minutos venceu enquanto estava oculta.
- **When:** a página volta a ficar visível.
- **Then:** uma nova tentativa é iniciada imediatamente; se o intervalo não venceu, nenhuma chamada adicional é iniciada por esse retorno.

**Cenário 3: atualizar manualmente sem chamadas simultâneas**
- **Given:** a aplicação está aberta e há uma cidade selecionada.
- **When:** o usuário aciona a atualização manual.
- **Then:** uma nova tentativa de consulta é iniciada sem esperar o próximo intervalo automático; se já houver uma tentativa manual ou automática em andamento para a mesma seção, a interface não inicia outra simultânea.

**Cenário 4: distinguir horários dos dados e da tentativa**
- **Given:** a fonte informa o horário de observação atual e uma consulta de previsão foi obtida com sucesso; depois ocorre uma nova tentativa.
- **When:** a interface mostra os horários relacionados aos dados.
- **Then:** o horário de observação, o horário de obtenção bem-sucedida da previsão e o horário da última tentativa são identificáveis separadamente.

**Cenário 5: não sobrepor atualizações da mesma seção**
- **Given:** uma consulta para clima atual ou previsão está em andamento para a cidade selecionada.
- **When:** vence o intervalo automático ou o usuário solicita atualização manual.
- **Then:** não se inicia uma segunda consulta simultânea para a mesma seção e cidade; a ação manual ainda pode iniciar a consulta da outra seção se ela estiver ociosa.

### AC-08 — Comunicar estados e falhas (FR-08)

**Cenário 1: comunicar carregamento**
- **Given:** uma consulta meteorológica foi iniciada e ainda não terminou.
- **When:** a seção correspondente é exibida.
- **Then:** a seção apresenta um estado de carregamento identificável.

**Cenário 2: comunicar indisponibilidade do provedor**
- **Given:** uma consulta meteorológica falha porque o provedor está indisponível ou excede o tempo limite.
- **When:** a consulta termina sem resposta utilizável.
- **Then:** a seção apresenta um estado de indisponibilidade identificável e oferece uma ação de nova tentativa.

**Cenário 3: comunicar erro na consulta**
- **Given:** o provedor retorna uma resposta de erro ou inválida para uma consulta meteorológica.
- **When:** a aplicação processa a resposta.
- **Then:** a seção apresenta um estado de erro identificável e oferece uma ação de nova tentativa.

**Cenário 4: comunicar ausência de resultados**
- **Given:** uma busca de cidade termina sem resultados.
- **When:** a lista de resultados é exibida.
- **Then:** a interface apresenta um estado vazio e orienta o usuário a refinar a consulta.

**Cenário 5: preservar seção que carregou em falha parcial**
- **Given:** a consulta de clima atual tem sucesso e a consulta de previsão falha, ou vice-versa.
- **When:** os resultados são exibidos.
- **Then:** a seção carregada continua acessível e somente a seção que falhou é identificada como indisponível.

**Cenário 6: oferecer nova tentativa após falha**
- **Given:** uma consulta de clima atual ou previsão falhou.
- **When:** o usuário ativa a ação de nova tentativa da seção com falha.
- **Then:** uma nova consulta para essa seção é iniciada.

**Cenário 7: identificar resposta meteorológica incompleta**
- **Given:** uma consulta retorna sem um ou mais dados essenciais definidos em FR-03 ou FR-04.
- **When:** a resposta é apresentada.
- **Then:** somente a seção ou os dias afetados são identificados como incompletos/indisponíveis; dados válidos de outras seções e dias permanecem acessíveis.

**Cenário 8: identificar dados vencidos sem apresentá-los como atuais**
- **Given:** o clima atual ultrapassou uma hora desde a observação ou a previsão ultrapassou seis horas desde a obtenção bem-sucedida, e os dados anteriores continuam visíveis.
- **When:** a atualização falha ou os dados vencem.
- **Then:** a seção mostra um aviso explícito e o horário dos dados; os dados não são identificados como atuais.

### AC-09 — Usar em dispositivos móveis (FR-09)

**Cenário 1: concluir consulta em telas móveis**
- **Given:** a aplicação está aberta em cada largura e orientação especificadas em NFR-01.
- **When:** o usuário pesquisa uma cidade, seleciona um resultado e consulta clima atual e previsão.
- **Then:** o fluxo pode ser concluído, os controles essenciais permanecem acessíveis e não há rolagem horizontal.

### AC-10 — Identificar fonte dos dados (FR-10)

**Cenário 1: encontrar atribuição do provedor**
- **Given:** o usuário navega até a seção Sobre/Créditos.
- **When:** essa seção é exibida.
- **Then:** o texto “Dados meteorológicos: Open-Meteo” está visível e Open-Meteo é um link para https://open-meteo.com/.

### AC-11 — Interação acessível

**Cenário 1: concluir fluxos por teclado**
- **Given:** a aplicação está aberta sem uso de mouse ou tela sensível ao toque.
- **When:** o usuário pesquisa, percorre e seleciona resultados, altera a unidade, atualiza dados e aciona uma nova tentativa.
- **Then:** todos os controles são alcançáveis e operáveis por teclado, a ordem de foco acompanha a ordem lógica, o foco permanece visível e não há armadilha de foco.

**Cenário 2: anunciar resultados e estados**
- **Given:** uma busca ou consulta meteorológica muda para carregamento, sucesso, vazio, erro ou dados incompletos.
- **When:** o estado é atualizado.
- **Then:** o estado e sua seção são identificáveis por tecnologia assistiva sem exigir que o usuário perceba somente uma mudança visual.

**Cenário 3: nomear controles**
- **Given:** os controles de busca, unidade, atualização, seleção de resultado e nova tentativa estão disponíveis.
- **When:** são inspecionados por leitor de tela.
- **Then:** cada controle tem nome acessível, papel e estado compreensíveis, e os resultados identificam os dados de localização disponíveis.

## Matriz de rastreabilidade

A tabela relaciona cada User Story aos critérios de aceite que a verificam e aos requisitos não funcionais que condicionam sua implementação ou validação.

| User Story | Acceptance Criteria relacionados | Requisitos não funcionais relevantes |
|---|---|---|
| **US-01 — Pesquisar e selecionar uma cidade** | AC-01 — Buscar cidades; AC-02 — Exibir e selecionar resultados | NFR-05 — Desempenho de busca; NFR-10 — Timeout de chamadas externas; NFR-11 — Privacidade e transporte |
| **US-02 — Consultar clima atual** | AC-03 — Consultar clima atual; AC-08 — Comunicar estados e falhas | NFR-03 — Clareza e localização; NFR-04 — Atualidade e consistência; NFR-09 — Fonte e uso dos dados; NFR-10 — Timeout de chamadas externas |
| **US-03 — Consultar previsão diária** | AC-04 — Consultar previsão diária; AC-08 — Comunicar estados e falhas | NFR-03 — Clareza e localização; NFR-04 — Atualidade e consistência; NFR-09 — Fonte e uso dos dados; NFR-10 — Timeout de chamadas externas |
| **US-04 — Alternar unidade de temperatura** | AC-05 — Alternar unidade de temperatura | NFR-03 — Clareza e localização; NFR-06 — Acessibilidade |
| **US-05 — Lembrar cidade e unidade** | AC-06 — Lembrar preferências | NFR-09 — Fonte e uso dos dados; NFR-11 — Privacidade e transporte |
| **US-06 — Atualizar dados e comunicar falhas ou vencimento** | AC-07 — Atualizar dados; AC-08 — Comunicar estados e falhas | NFR-04 — Atualidade e consistência; NFR-07 — Disponibilidade; NFR-09 — Fonte e uso dos dados; NFR-10 — Timeout de chamadas externas |
| **US-07 — Consultar pelo celular** | AC-09 — Usar em dispositivos móveis; AC-01 a AC-08 — etapas do fluxo de consulta | NFR-01 — Responsividade; NFR-08 — Compatibilidade |
| **US-08 — Identificar a fonte dos dados** | AC-10 — Identificar fonte dos dados | NFR-09 — Fonte e uso dos dados |

**Rastreabilidade transversal:** AC-11 — Interação acessível aplica-se aos fluxos interativos de US-01 a US-07 e é validado por NFR-06 — Acessibilidade. NFR-02 — Usabilidade avalia as tarefas de busca e seleção (US-01), consulta do clima atual (US-02), previsão (US-03), alternância de unidade (US-04) e atualização (US-06). NFR-01 — Responsividade e NFR-08 — Compatibilidade também devem ser considerados ao validar a interface dos fluxos cobertos pelas histórias correspondentes.

## Non-Functional Requirements

- **NFR-01 — Responsividade:** entre 320 px e 1440 px de largura de viewport CSS, adaptar conteúdo e controles sem rolagem horizontal nem perda de funcionalidades. Validar nos viewports de 320, 375, 768, 1024 e 1440 px, em retrato e paisagem. Também validar zoom de navegador de 200% em viewport inicial de 1280 × 720 CSS px; nesse caso, o conteúdo deve refluir sem exigir rolagem horizontal. Em cada configuração, concluir busca, seleção, leitura do clima e previsão; campo, botão de busca, unidade, atualização e ações de nova tentativa devem permanecer visíveis ou alcançáveis por rolagem vertical.
- **NFR-02 — Usabilidade:** realizar uma validação interna com cinco participantes da equipe, sem participantes externos, usando o mesmo roteiro: (1) buscar e selecionar uma cidade brasileira; (2) localizar temperatura e horário de observação; (3) localizar a previsão para os próximos dias; (4) alternar a unidade e atualizar os dados. Uma tarefa é concluída sem ajuda quando o participante atinge o resultado descrito sem instrução ou intervenção do moderador. A validação passa se pelo menos quatro dos cinco participantes concluírem sem ajuda cada tarefa e ninguém ficar bloqueado de consultar clima atual ou previsão. Registrar resultado por participante e tarefa e os problemas observados; não apresentar o resultado como evidência de usabilidade com o público geral.
- **NFR-03 — Clareza e localização:** cada conjunto de clima atual e previsão identifica claramente cidade e período. Datas e números seguem padrões pt-BR; horários usam relógio de 24 horas e identificam o fuso IANA e o deslocamento UTC local da cidade consultada.
- **NFR-04 — Atualidade e consistência:** o clima atual é válido por até 60 minutos desde a observação indicada pela fonte; a previsão é válida por até seis horas desde a obtenção bem-sucedida pelo app. Os limites são inclusivos: os dados ainda são válidos exatamente no limite e vencem depois dele. Horários devem ser interpretáveis e não podem estar no futuro; timestamp ausente, inválido ou futuro torna o dado essencial correspondente incompleto/indisponível. Dados vencidos podem permanecer visíveis somente com aviso explícito e horário, nunca identificados como atuais. Uma falha parcial afeta apenas a seção correspondente. O horário da última tentativa não substitui nem altera os horários de observação/obtenção.
- **NFR-05 — Desempenho de busca:** em cada janela de sete dias de validação, medir pelo menos 100 envios aceitos (2–100 pontos de código Unicode) de ponta a ponta, incluindo o provedor, desde o envio até resultados, estado vazio ou erro terminal ficarem visíveis. O percentil 95 deve ser de até dois segundos; tentativas com erro e timeout permanecem na amostra. Fazer as medições em um mesmo celular Android de faixa intermediária, com Chrome estável atual e conexão 4G no Brasil; registrar aparelho, sistema operacional, versões, região, rede, amostras e política de cache antes da medição. Distribuir pelo menos 30 amostras por região em no mínimo três regiões brasileiras. Desabilitar o cache do navegador, sem alterar a política de cache do provedor. A meta aplica-se à busca de cidades; este MVP não define meta de latência para consultas meteorológicas.
- **NFR-06 — Acessibilidade:** atender aos critérios aplicáveis de nível AA da WCAG 2.2 em busca, resultados, dados meteorológicos, unidade, atualização e estados de erro. A evidência inclui auditoria automática, revisão manual de cada apontamento e testes dos fluxos completos por teclado (sem armadilha de foco e com foco visível) e leitor de tela (NVDA com Firefox em desktop e VoiceOver com Safari em iOS). Não pode haver violação confirmada e não resolvida de critério aplicável WCAG 2.2 A ou AA. Registrar ferramenta, versões, páginas/estados testados e violações; qualquer exceção deve ser documentada e aprovada antes do lançamento.
- **NFR-07 — Disponibilidade:** 99,5% mensal é objetivo futuro, não uma meta mensurável para o MVP. Durante falhas externas, cumprir FR-08 e AC-08.
- **NFR-08 — Compatibilidade:** suportar as versões estável atual e imediatamente anterior disponíveis na data de cada ciclo: Chrome, Edge e Firefox em Windows e macOS; Safari em macOS; Chrome e Firefox em Android; Safari em iOS. Registrar no relatório a data do ensaio, sistema operacional e versão exata de cada navegador/dispositivo. Uma combinação sem versão estável suportada pelo fabricante é excluída e registrada como tal.
- **NFR-09 — Fonte e uso dos dados:** antes do lançamento, validar cobertura no Brasil, campos necessários, frequência de atualização, limites e condições de uso do Open-Meteo. A atribuição deve estar disponível em Sobre/Créditos. Não manter cache meteorológico local entre sessões; qualquer cache temporário em memória não pode estender as validades de FR-07/NFR-04.
- **NFR-10 — Timeout de chamadas externas:** encerrar chamadas de geocodificação após 8 segundos e chamadas meteorológicas após 10 segundos. Ao exceder o limite, exibir indisponibilidade e opção de nova tentativa; o timeout de uma seção não deve bloquear outra.
- **NFR-11 — Privacidade e transporte:** enviar aos provedores somente o texto de busca, as coordenadas e os parâmetros necessários às consultas solicitadas. Não solicitar nem acessar localização do dispositivo, não exigir conta e não persistir histórico de buscas nem respostas meteorológicas. Guardar localmente somente a unidade escolhida e os dados mínimos para restaurar a última cidade. Todas as chamadas a provedores devem usar HTTPS; nenhum segredo ou credencial deve ser incluído no cliente.

## Edge Cases

- **Cidade sem correspondência ou cobertura:** não selecionar local artificial. Sem resultados, manter a cidade selecionada anteriormente; se um local não tiver coordenadas ou dados meteorológicos utilizáveis, informar indisponibilidade sem substituir seus dados pelos de outra cidade.
- **Resultados ambíguos ou parciais:** manter a ordem de relevância do provedor; exibir apenas os campos de localização fornecidos. Resultados sem nome ou coordenadas válidas não podem iniciar consulta meteorológica.
- **Troca de busca ou cidade com requisições em andamento:** respostas antigas não atualizam a busca ou cidade selecionada mais recentemente. Se dados anteriores permanecerem visíveis durante a carga, continuam identificados com a cidade a que pertencem.
- **Virada de data e fuso:** calcular as cinco datas no fuso da cidade, inclusive durante mudança de dia ou horário de verão; o primeiro dia pode ser parcial.
- **Expiração durante falha ou página oculta:** manter dados somente com aviso e horário; ao retornar à página, aplicar a regra de atualização vencida de FR-07.
- **Preferências indisponíveis ou inválidas:** manter a consulta utilizável sem persistência e usar Celsius se a unidade salva não for válida.
- **Atualizações concorrentes:** uma tentativa em andamento, manual ou automática, não pode ser duplicada para a mesma seção e cidade; a outra seção pode atualizar independentemente.
- **Tela estreita, orientação ou zoom:** os fluxos permanecem operáveis e sem rolagem horizontal nas condições definidas em NFR-01.

## Assumptions

- A aplicação atende uma cidade selecionada por vez e não oferece conta, sincronização nem localização automática.
- A cobertura e disponibilidade dos campos do Open-Meteo variam por local e devem ser confirmadas antes do lançamento, conforme os gates abaixo.
- A validação interna de usabilidade não representa usuários externos nem o público geral.

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

## Release Gates

1. **Compatibilidade:** antes do lançamento, executar a matriz de NFR-08 e registrar data, versões exatas e resultados.
2. **Desempenho:** executar a amostragem de sete dias de NFR-05. O gate passa somente com pelo menos 100 envios medidos e P95 de até dois segundos.
3. **Usabilidade interna:** executar o roteiro de NFR-02. O gate passa com pelo menos quatro de cinco participantes concluindo sem ajuda cada tarefa e sem bloqueio de consulta; registrar resultados e problemas.
4. **Provedor — bloqueio de lançamento:** confirmar e registrar cobertura brasileira, campos essenciais, fuso local, frequência de atualização, limites, atribuição e condições de uso do Open-Meteo. Não lançar se os dados essenciais ou os termos de uso não suportarem o MVP.
