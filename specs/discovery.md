## Contexto

A empresa precisa de uma aplicação de previsão do tempo que permita aos usuários consultar as condições meteorológicas de cidades de interesse. A experiência deve funcionar em dispositivos móveis e apresentar tanto o clima atual quanto a previsão para cinco períodos consecutivos de 24 horas.

O MVP considera busca manual de cidades no Brasil, consulta do clima atual e previsão em períodos diários, além da alternância entre Celsius e Fahrenheit. A fonte dos dados foi definida como Open-Meteo.

## Requisitos Funcionais

- **RF-01 — Buscar cidades:** o usuário deve poder pesquisar uma cidade pelo nome e selecionar o local desejado entre os resultados.
- **RF-02 — Consultar clima atual:** para a cidade selecionada, a aplicação deve exibir as condições meteorológicas atuais.
- **RF-03 — Consultar previsão:** a aplicação deve exibir cinco períodos consecutivos de 24 horas a partir da consulta para a cidade selecionada.
- **RF-04 — Alternar unidade de temperatura:** o usuário deve poder alternar entre Celsius e Fahrenheit; as temperaturas apresentadas devem refletir a unidade escolhida.
- **RF-05 — Consultar em dispositivos móveis:** o usuário deve poder buscar cidades, selecionar um local, consultar o clima atual e visualizar a previsão em um dispositivo móvel.
- **RF-06 — Informar falhas de consulta:** quando os dados meteorológicos não estiverem disponíveis ou não puderem ser atualizados, a aplicação deve informar a situação ao usuário e não apresentar dados desatualizados como atuais.

## Requisitos Não-Funcionais

- **RNF-01 — Responsividade:** entre 320 px e 1440 px, o conteúdo e os controles devem se adaptar sem rolagem horizontal nem perda de funcionalidades. Validar em 320, 375, 768, 1024 e 1440 px, em retrato e paisagem, incluindo zoom de 200%.
- **RNF-02 — Usabilidade:** busca, seleção de cidade e alternância de unidade devem ter rótulos e estados compreensíveis. No MVP, a equipe fará uma revisão interna por checklist e deverá concluir sem ajuda as tarefas de pesquisar uma cidade, selecionar um resultado, consultar clima/previsão e trocar a unidade; não haverá teste com usuários.
- **RNF-03 — Clareza dos dados:** cada conjunto de condições atuais e previsão deve identificar claramente a cidade e o período correspondente. Datas e horários seguem os padrões pt-BR, usam relógio de 24 horas e identificam o fuso horário local da cidade consultada.
- **RNF-04 — Atualidade dos dados:** exibir o horário da consulta e indicar quando os dados não puderem ser confirmados como atuais. Considerar o clima atual válido por até 1 hora e a previsão por até 6 horas; dados vencidos exibidos durante falhas devem trazer aviso e nunca ser apresentados como atuais.
- **RNF-05 — Desempenho:** em uma janela de 7 dias, o percentil 95 das buscas deve apresentar resultados em até 2 segundos, medidos ponta a ponta no navegador desde o envio da busca até os resultados ficarem visíveis, incluindo o provedor. Usar celular de faixa intermediária, navegador móvel atual e conexão 4G no Brasil.
- **RNF-06 — Acessibilidade:** atender ao nível AA da WCAG 2.2. A evidência incluirá auditoria automática e testes manuais de teclado, foco e leitor de tela; violações aplicáveis devem ser corrigidas.
- **RNF-07 — Disponibilidade:** 99,5% ao mês permanece como objetivo futuro, sem meta mensurável no MVP. Durante falhas externas, o app deve cumprir o RF-06.
- **RNF-08 — Compatibilidade:** suportar versões atuais e imediatamente anteriores de Chrome, Edge, Firefox e Safari em desktop e dispositivos móveis.

## Riscos

As probabilidades abaixo são estimativas qualitativas para o discovery; devem ser revistas quando o provedor, o público-alvo e os critérios de aceite forem definidos.

| Categoria | Risco | Probabilidade | Impacto | Estratégia de mitigação |
|---|---|---|---|---|
| Técnico | Indisponibilidade, lentidão ou limites do provedor de dados impedirem consultas ou degradarem a experiência. | Média | Alto: usuários podem ficar sem clima atual ou previsão. | Verificar cobertura, limites e frequência de atualização do Open-Meteo; definir timeouts e tratamento de falhas; informar a indisponibilidade e o horário da última atualização sem apresentar dados antigos como atuais. |
| Técnico / Produto | Dados desatualizados ou períodos interpretados incorretamente por causa do fuso horário da cidade. | Média | Alto: informações podem parecer atuais ou corresponder ao dia errado. | Definir a validade dos dados e usar o fuso local da cidade; mostrar o horário da última atualização e o período da previsão; testar viradas de data e dados vencidos. |
| Produto | Períodos de 24 horas podem ser interpretados como dias do calendário, embora a previsão tenha sido definida a partir do momento da consulta. | Média | Médio/alto: usuários podem entender datas e limites dos períodos de forma diferente. | Identificar claramente o início e o fim de cada período e validar a apresentação conforme o fuso local da cidade. |
| Técnico / Produto | Resultados de cidades homônimas ou cobertura geográfica insuficiente levarem à seleção do local errado. | Média | Alto: o usuário recebe informações meteorológicas de outra cidade. | Definir as regiões atendidas e exibir país e estado/região nos resultados; estabelecer regras para ordenação, ausência e excesso de resultados. |
| Técnico | A busca pode não cumprir a meta P95 de até 2 segundos, medida ponta a ponta incluindo o provedor. | Média | Médio/alto: a experiência pode parecer lenta, especialmente em redes móveis. | Medir no ambiente definido no RNF-05; otimizar o carregamento e evitar consultas desnecessárias enquanto o usuário digita. |
| Técnico / Produto | A interface pode falhar em telas, orientações, navegadores ou tecnologias assistivas não contemplados nos testes. | Média | Alto: alguns usuários podem não conseguir concluir tarefas. | Definir a matriz de dispositivos e navegadores; testar as larguras e orientações previstas; validar teclado, foco, contraste e nomes acessíveis em relação à WCAG 2.2 nível AA. |
| Produto | O indicador de sucesso do MVP é qualitativo e funcionalidades fora do escopo podem gerar expectativas adicionais. | Média | Alto: prioridades podem mudar durante o desenvolvimento e o produto pode não atender à necessidade principal. | Manter explícitos o público, o escopo do MVP e o indicador qualitativo; definir métricas quantitativas se forem necessárias para avaliar o lançamento. |
| Técnico / Produto | A disponibilidade não será medida no MVP e a revisão de usabilidade será interna. | Média | Médio/alto: não será possível comprovar objetivamente disponibilidade mensal nem usabilidade com usuários reais. | Tratar 99,5% como objetivo futuro e planejar monitoramento e testes com usuários caso esses critérios se tornem requisitos de lançamento. |

## Perguntas levantadas no discovery

As perguntas abaixo registram os pontos investigados originalmente. As respostas e decisões da entrevista estão consolidadas na seção **Decisões**.

Os impactos descrevem os riscos identificados antes das decisões.

### Produto e escopo

1. **Pergunta em aberto:** Quem é o público-alvo e em quais situações ele usará o app?<br>
   **Impacto:** sem conhecer usuários e contexto de uso, fica difícil priorizar informações, linguagem, acessibilidade e decisões como localização automática.

2. **Pergunta em aberto:** “Cidades de interesse” significa uma lista persistente de favoritos ou apenas a cidade selecionada na consulta atual?<br>
   **Impacto:** a resposta muda navegação, persistência de dados, interface e escopo de implementação. O briefing assume uma cidade por vez, mas não confirma se isso limita a experiência.

3. **Pergunta em aberto:** Quais funcionalidades estão explicitamente fora do escopo inicial?<br>
   **Impacto:** sem limites claros, itens como alertas meteorológicos, localização automática, favoritos e previsão horária podem surgir como expectativas durante a implementação.

4. **Pergunta em aberto:** Existe algum objetivo de negócio ou indicador de sucesso para o lançamento?<br>
   **Impacto:** sem uma medida de sucesso, não será possível avaliar se o produto atende à necessidade, além de cumprir requisitos técnicos.

### Busca e seleção de cidades

5. **Pergunta em aberto:** Quais entradas a busca deve aceitar: nome, estado, país, código postal ou localização do dispositivo?<br>
   **Impacto:** define o comportamento esperado, o provedor necessário e como o usuário encontrará locais fora do seu país.

6. **Pergunta em aberto:** Em quais países ou regiões a busca deve funcionar?<br>
   **Impacto:** cobertura geográfica, idiomas de busca, formatos de endereço e qualidade dos resultados podem variar por região.

7. **Pergunta em aberto:** Como o usuário deve diferenciar cidades homônimas? Os resultados devem exibir país, estado/região, coordenadas ou outro identificador?<br>
   **Impacto:** sem contexto suficiente, o usuário pode selecionar a cidade errada e receber dados incorretos para sua intenção.

8. **Pergunta em aberto:** Qual deve ser o comportamento da busca enquanto o usuário digita? Há busca automática, botão de envio, limite de caracteres, tolerância a erros de digitação e regra para consultas vazias?<br>
   **Impacto:** sem essas definições, a experiência pode ser inconsistente e gerar consultas desnecessárias ou resultados confusos.

9. **Pergunta em aberto:** O que acontece quando não há resultados ou quando há muitos resultados?<br>
   **Impacto:** faltam estados de vazio, orientação para refinar a busca e regras de ordenação ou paginação.

10. **Pergunta em aberto:** O app deve solicitar permissão de localização? Se sim, quando, com qual justificativa e qual alternativa haverá em caso de recusa?<br>
    **Impacto:** afeta privacidade, permissões, fluxo inicial e experiência de quem não concede acesso.

### Dados meteorológicos e apresentação

11. **Decidida:** será usado o Open-Meteo, sem API key.<br>
    **Justificativa e impacto:** a fonte fica definida sem exigir configuração de chave de API. Cobertura de cidades, frequência de atualização e demais limitações do provedor ainda precisam ser verificadas.

12. **Pergunta em aberto:** O que exatamente significa “clima atual”? Quais variáveis devem ser exibidas e qual período ou observação representa o “atual”?<br>
    **Impacto:** sem definição, produto e implementação podem discordar sobre o conteúdo mínimo e sobre quando o dado está atualizado.

13. **Pergunta em aberto:** Quais informações devem aparecer na previsão, além de temperatura e condição do tempo?<br>
    **Impacto:** afeta o layout, a legibilidade em dispositivos móveis e a possibilidade de comparar os dias.

14. **Decidida:** “previsão para os próximos cinco dias” significa hoje e os quatro dias seguintes.<br>
    **Justificativa e impacto:** fixa o período da previsão e elimina a ambiguidade sobre incluir hoje; o primeiro dia pode ser parcial, conforme o horário da consulta.

15. **Pergunta em aberto:** A previsão será diária, horária ou terá as duas granularidades?<br>
    **Impacto:** muda a quantidade de informação, o design, os dados exigidos do provedor e o significado prático da previsão.

16. **Pergunta em aberto:** Qual intervalo e qual definição de “dia” serão usados na previsão diária? O dia segue o calendário local da cidade? Como são tratados o primeiro dia parcial e a virada de data?<br>
    **Impacto:** pode haver divergência entre as datas exibidas e os períodos que o usuário considera como “hoje” e “amanhã”.

17. **Parcialmente decidida:** a interface será em pt-BR; os formatos de condições, datas, horas e números ainda estão em aberto.<br>
    **Impacto:** o briefing não define idioma, formatação regional nem se haverá suporte a vários idiomas; isso pode tornar rótulos e datas ambíguos.

18. **Pergunta em aberto:** Qual fuso horário será aplicado e como será identificado: o da cidade consultada, o do dispositivo ou outro?<br>
    **Impacto:** uma escolha inconsistente pode deslocar a previsão em relação ao local, mesmo que o fuso seja indicado na interface.

19. **Pergunta em aberto:** Quais unidades se aplicam além da temperatura?<br>
    **Impacto:** alternar Celsius/Fahrenheit não esclarece unidades para vento, precipitação ou distância. Misturar padrões pode confundir usuários de diferentes regiões.

20. **Pergunta em aberto:** O sistema deve exibir ícones, descrições textuais ou ambos para as condições do tempo?<br>
    **Impacto:** sem essa definição, a compreensão pode depender de símbolos ambíguos ou informações que não sejam acessíveis para todos.

21. **Pergunta em aberto:** Alertas, avisos de tempo severo ou recomendações fazem parte do produto ou estão fora do escopo?<br>
    **Impacto:** a decisão pode afetar dados, apresentação e expectativas de segurança do usuário.

### Atualização, falhas e persistência

22. **Pergunta em aberto:** Qual é o tempo máximo aceitável para considerar os dados válidos?<br>
    **Impacto:** a regra depende do provedor e precisa distinguir clima atual de previsão; sem ela, o app não sabe quando sinalizar que os dados podem estar desatualizados.

23. **Pergunta em aberto:** Dados antigos devem ser ocultados, exibidos com horário e aviso, ou tratados de outra forma?<br>
    **Impacto:** a exigência de não rotular dados antigos como atuais não determina o que o usuário verá durante uma falha, podendo levar a comportamentos incompatíveis entre telas.

24. **Pergunta em aberto:** Como devem ser tratados carregamento, falha parcial, ausência de rede e indisponibilidade do provedor?<br>
    **Impacto:** o RF-06 prevê informar falhas, mas não define mensagens, ações de recuperação, apresentação de dados parcialmente disponíveis ou comportamento offline.

25. **Pergunta em aberto:** Com que frequência os dados serão atualizados e o usuário poderá atualizá-los manualmente?<br>
    **Impacto:** sem regras de atualização, podem ocorrer consultas excessivas, consumo desnecessário de recursos ou dados menos recentes do que o esperado.

26. **Pergunta em aberto:** A cidade selecionada e a unidade escolhida serão mantidas entre sessões? E entre dispositivos?<br>
    **Impacto:** a persistência da unidade está em aberto e a da cidade não foi especificada. Sem decisão, o app pode perder preferências ou criar expectativas incompatíveis sobre contas e sincronização.

27. **Pergunta em aberto:** O provedor exige atribuição, impõe restrições de uso ou condiciona o armazenamento dos dados?<br>
    **Impacto:** a escolha de fonte pode exigir créditos visíveis, limitar cache ou afetar a viabilidade legal e operacional da experiência.

### Qualidade, critérios e operação

28. **Pergunta em aberto:** Quais tarefas, participantes e critérios determinam que busca, seleção e troca de unidade são compreensíveis e concluídas sem ajuda?<br>
    **Impacto:** “sem instruções externas” é uma intenção, mas não permite verificar objetivamente se o requisito foi atendido.

29. **Pergunta em aberto:** Como será medida a meta de desempenho de 95% das consultas em até 2 segundos? Qual ambiente, região, dispositivo, tipo de rede, período de medição e início/fim da contagem serão usados?<br>
    **Impacto:** excluir demora do provedor não define como separar esse tempo do restante nem permite comparar resultados de forma consistente. A métrica pode parecer cumprida sem refletir a experiência real.

30. **Pergunta em aberto:** Como será calculada a disponibilidade mensal de 99,5%? Qual serviço ou fluxo será monitorado, quais falhas serão excluídas e como serão contabilizadas falhas do provedor?<br>
    **Impacto:** sem método de medição e exclusões acordados, não há como verificar a meta nem distinguir indisponibilidade do app de degradação externa.

31. **Pergunta em aberto:** Quais navegadores, sistemas operacionais e versões serão suportados?<br>
    **Impacto:** “dispositivos móveis e desktop” não define uma matriz testável; a cobertura pode variar entre equipes e deixar usuários sem suporte.

32. **Pergunta em aberto:** Como serão testados os requisitos de responsividade e orientação? Os limites de 320–1440 px e as orientações retrato/paisagem cobrem as combinações necessárias? Há expectativas para zoom ou telas fora desse intervalo?<br>
    **Impacto:** os requisitos atuais deixam dimensões e condições de teste indefinidas, apesar de exigirem ausência de rolagem horizontal e perda de funcionalidades.

33. **Pergunta em aberto:** Qual evidência será usada para comprovar conformidade com WCAG 2.2 nível AA?<br>
    **Impacto:** listar teclado, foco e contraste não cobre, por si só, todos os critérios aplicáveis; sem método de verificação, “AA” pode ser interpretado de formas diferentes.

**Validações antes do lançamento:** confirmar cobertura, limites, condições de uso e atribuição do Open-Meteo; validar a viabilidade da previsão em períodos consecutivos de 24 horas; e executar as verificações de desempenho, compatibilidade e acessibilidade definidas acima.

## Decisões

- **Produto e escopo (perguntas 1–4):** público geral em consultas ocasionais; uma cidade por vez. O MVP inclui busca manual, clima atual e previsão diária; localização automática, favoritos, previsão horária e alertas ficam fora do escopo. A cidade e a unidade serão lembradas localmente, sem contas ou sincronização entre dispositivos. O indicador de sucesso é permitir consultar diferentes cidades e ver seu clima; não foi definido um indicador quantitativo.
- **Busca (perguntas 5–10):** aceitar nome da cidade, com cobertura no Brasil. Exibir cidade, estado/região e país para desambiguar. A busca é enviada por Enter ou botão, a partir de dois caracteres; ordenar por relevância, limitar a dez resultados e orientar o usuário a refinar quando não houver resultados. Não solicitar localização do dispositivo.
- **Fonte de dados (perguntas 11 e 27):** Open-Meteo, sem API key. Exibir atribuição na seção Sobre/Créditos. Antes do lançamento, validar cobertura, limites e condições de uso; limitar o cache local às validades definidas nesta especificação.
- **Clima e previsão (perguntas 12–16):** clima atual com temperatura, condição textual, sensação térmica, umidade, vento e precipitação. Exibir ícone e descrição textual. A previsão terá somente visualização diária para cinco períodos consecutivos de 24 horas a partir da consulta, no fuso local da cidade, com temperaturas mínima/máxima, condição, probabilidade/volume de chuva e vento.
- **Idioma, horários e unidades (perguntas 17–19):** interface em pt-BR; datas e números em padrões pt-BR e relógio de 24 horas. Usar o fuso local da cidade consultada e identificá-lo. Celsius é a unidade padrão de temperatura, com alternância para Fahrenheit; vento em km/h e precipitação em mm.
- **Alertas (pergunta 21):** alertas meteorológicos, avisos de tempo severo e recomendações ficam fora do MVP.
- **Atualização e falhas (perguntas 22–25):** validade de até 1 hora para clima atual e 6 horas para previsão. Se uma atualização falhar, exibir dados vencidos com horário e aviso, nunca como atuais; mostrar as seções que carregaram e indicar as que falharam, com ação para tentar novamente. Atualizar automaticamente a cada 30 minutos e oferecer atualização manual.
- **Usabilidade e desempenho (perguntas 28–29):** sem testes com usuários no MVP; a equipe revisará um checklist e deverá concluir sem ajuda a busca, seleção, consulta e troca de unidade. Medir a busca ponta a ponta, incluindo o provedor, por 7 dias; meta P95 de até 2 segundos em celular de faixa intermediária, navegador móvel atual e 4G no Brasil, do envio até os resultados visíveis.
- **Disponibilidade, compatibilidade e acessibilidade (perguntas 30–33):** 99,5% mensal é objetivo futuro, sem meta mensurável no MVP. Suportar as versões atuais e anteriores de Chrome, Edge, Firefox e Safari em desktop e mobile. Validar os tamanhos e orientações especificados no RNF-01. Para WCAG 2.2 AA, combinar auditoria automática com testes manuais de teclado, foco e leitor de tela, corrigindo violações aplicáveis.

## Suposições (Assumptions)

- O usuário informa a cidade pela busca; localização automática não é requisito confirmado.
- A aplicação apresenta dados de uma cidade selecionada por vez e lembra localmente a última cidade consultada e a unidade escolhida.
- Celsius e Fahrenheit se aplicam a todas as temperaturas exibidas.
- A previsão abrange cinco períodos consecutivos de 24 horas a partir do momento da consulta.
- Os dados meteorológicos serão obtidos do Open-Meteo.
- A interface será em pt-BR e suportará as versões atuais e anteriores de Chrome, Edge, Firefox e Safari em desktop e mobile.