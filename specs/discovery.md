## Contexto

A empresa precisa de uma aplicação de previsão do tempo que permita aos usuários consultar as condições meteorológicas de cidades de interesse. A experiência deve funcionar em dispositivos móveis e apresentar tanto o clima atual quanto a previsão para os próximos cinco dias.

O discovery considera como escopo inicial a busca de cidades, a consulta das informações meteorológicas e a alternância entre Celsius e Fahrenheit. A fonte dos dados, a cobertura geográfica e outros detalhes de produto ainda precisam ser definidos.

## Requisitos Funcionais

- **RF-01 — Buscar cidades:** o usuário deve poder pesquisar uma cidade pelo nome e selecionar o local desejado entre os resultados.
- **RF-02 — Consultar clima atual:** para a cidade selecionada, a aplicação deve exibir as condições meteorológicas atuais.
- **RF-03 — Consultar previsão:** a aplicação deve exibir a previsão para os próximos cinco dias da cidade selecionada.
- **RF-04 — Alternar unidade de temperatura:** o usuário deve poder alternar entre Celsius e Fahrenheit; as temperaturas apresentadas devem refletir a unidade escolhida.
- **RF-05 — Consultar em dispositivos móveis:** o usuário deve poder buscar cidades, selecionar um local, consultar o clima atual e visualizar a previsão em um dispositivo móvel.
- **RF-06 — Informar falhas de consulta:** quando os dados meteorológicos não estiverem disponíveis ou não puderem ser atualizados, a aplicação deve informar a situação ao usuário e não apresentar dados desatualizados como atuais.

## Requisitos Não-Funcionais

- **RNF-01 — Responsividade:** em larguras de tela entre 320 px e 1440 px, o conteúdo e os controles devem se adaptar sem rolagem horizontal da página nem perda de funcionalidades. Validar também em orientações retrato e paisagem.
- **RNF-02 — Usabilidade:** busca, seleção de cidade e alternância de unidade devem ter rótulos e estados compreensíveis, e as tarefas principais devem poder ser concluídas sem instruções externas. Os critérios de avaliação de usabilidade precisam ser definidos.
- **RNF-03 — Clareza dos dados:** cada conjunto de condições atuais e previsão deve identificar claramente a cidade e o período correspondente; datas e horários devem indicar o fuso horário aplicável.
- **RNF-04 — Atualidade dos dados:** a aplicação deve exibir quando os dados foram atualizados e indicar quando não puder confirmá-los como atuais. O tempo máximo de validade dos dados precisa ser definido de acordo com o provedor escolhido.
- **RNF-05 — Desempenho:** em condições de rede e dispositivo a definir, 95% das buscas e consultas devem apresentar resultado em até 2 segundos, desconsiderando falhas ou demora do provedor externo. A métrica e o ambiente de medição devem ser validados.
- **RNF-06 — Acessibilidade:** a interface deve atender ao nível AA da WCAG 2.2, incluindo operação por teclado, foco visível, contraste adequado e nomes acessíveis para controles e informações relevantes.
- **RNF-07 — Disponibilidade:** a aplicação deve atingir disponibilidade mensal de 99,5% para suas funcionalidades, excluindo indisponibilidades do provedor externo; durante falhas externas, deve cumprir o RF-06. A forma de medição e exclusões devem ser acordadas.
- **RNF-08 — Compatibilidade:** a aplicação deve funcionar nos navegadores e sistemas operacionais móveis e desktop definidos como suportados. A matriz de versões suportadas precisa ser acordada.

## Riscos

As probabilidades abaixo são estimativas qualitativas para o discovery; devem ser revistas quando o provedor, o público-alvo e os critérios de aceite forem definidos.

| Categoria | Risco | Probabilidade | Impacto | Estratégia de mitigação |
|---|---|---|---|---|
| Técnico | Indisponibilidade, lentidão ou limites do provedor de dados impedirem consultas ou degradarem a experiência. | Média | Alto: usuários podem ficar sem clima atual ou previsão. | Confirmar cobertura, limites e frequência de atualização do provedor; definir timeouts e tratamento de falhas; informar a indisponibilidade e o horário da última atualização sem apresentar dados antigos como atuais. |
| Técnico / Produto | Dados desatualizados ou períodos interpretados incorretamente por causa do fuso horário da cidade. | Média | Alto: informações podem parecer atuais ou corresponder ao dia errado. | Definir a validade dos dados e usar o fuso local da cidade; mostrar o horário da última atualização e o período da previsão; testar viradas de data e dados vencidos. |
| Produto | “Próximos cinco dias” e o nível de detalhe da previsão não estão definidos. | Alta | Médio/alto: produto e usuários podem esperar períodos ou informações diferentes. | Decidir se hoje está incluído e se a previsão será diária, horária ou ambas; registrar a decisão nos critérios de aceite e validá-la com usuários. |
| Técnico / Produto | Resultados de cidades homônimas ou cobertura geográfica insuficiente levarem à seleção do local errado. | Média | Alto: o usuário recebe informações meteorológicas de outra cidade. | Definir as regiões atendidas e exibir país e estado/região nos resultados; estabelecer regras para ordenação, ausência e excesso de resultados. |
| Técnico | Busca e consultas podem não cumprir a meta de desempenho de 95% em até 2 segundos. | Média | Médio/alto: a experiência pode parecer lenta, especialmente em redes móveis. | Definir ambiente e método de medição; medir separadamente o tempo do app e o do provedor; otimizar o carregamento e evitar consultas desnecessárias enquanto o usuário digita. |
| Técnico / Produto | A interface pode falhar em telas, orientações, navegadores ou tecnologias assistivas não contemplados nos testes. | Média | Alto: alguns usuários podem não conseguir concluir tarefas. | Definir a matriz de dispositivos e navegadores; testar as larguras e orientações previstas; validar teclado, foco, contraste e nomes acessíveis em relação à WCAG 2.2 nível AA. |
| Produto | Público-alvo, objetivos de sucesso e escopo inicial não estão definidos; podem surgir expectativas de favoritos, localização automática ou alertas. | Alta | Alto: prioridades podem mudar durante o desenvolvimento e o produto pode não atender à necessidade principal. | Identificar usuários e tarefas prioritárias; definir indicadores de sucesso e itens fora do escopo; validar se a cidade e a unidade devem persistir entre sessões. |
| Técnico / Produto | Metas de disponibilidade e usabilidade não têm método de avaliação acordado. | Média | Médio/alto: não será possível saber objetivamente se o app atende aos requisitos. | Especificar como calcular disponibilidade e usabilidade, incluindo cenários e exclusões; criar critérios mensuráveis e monitoramento antes do lançamento. |

## Perguntas em Aberto e Impactos

Os pontos abaixo precisam de resposta antes de fechar escopo, critérios de aceite ou decisões de implementação. O impacto descreve o risco de prosseguir sem uma decisão.

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

11. **Pergunta em aberto:** Qual provedor será usado e quais são suas limitações relevantes?<br>
    **Impacto:** cobertura de cidades, frequência de atualização, limites de uso, confiabilidade, formato dos dados e eventuais custos dependem dessa escolha.

12. **Pergunta em aberto:** O que exatamente significa “clima atual”? Quais variáveis devem ser exibidas e qual período ou observação representa o “atual”?<br>
    **Impacto:** sem definição, produto e implementação podem discordar sobre o conteúdo mínimo e sobre quando o dado está atualizado.

13. **Pergunta em aberto:** Quais informações devem aparecer na previsão, além de temperatura e condição do tempo?<br>
    **Impacto:** afeta o layout, a legibilidade em dispositivos móveis e a possibilidade de comparar os dias.

14. **Pergunta em aberto:** “Previsão para os próximos cinco dias” inclui hoje ou os cinco dias completos seguintes?<br>
    **Impacto:** pode resultar em períodos diferentes para usuários que consultam o app em horários distintos, dificultando comparação e validação.

15. **Pergunta em aberto:** A previsão será diária, horária ou terá as duas granularidades?<br>
    **Impacto:** muda a quantidade de informação, o design, os dados exigidos do provedor e o significado prático da previsão.

16. **Pergunta em aberto:** Qual intervalo e qual definição de “dia” serão usados na previsão diária? O dia segue o calendário local da cidade? Como são tratados o primeiro dia parcial e a virada de data?<br>
    **Impacto:** pode haver divergência entre as datas exibidas e os períodos que o usuário considera como “hoje” e “amanhã”.

17. **Pergunta em aberto:** Em que idioma e formato serão apresentados condições, datas, horas e números?<br>
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

**Prioridade para fechar escopo e aceite:** cobertura e desambiguação da busca; definição do período e conteúdo da previsão; tratamento de dados vencidos ou indisponíveis; persistência de preferências; e critérios mensuráveis para desempenho, disponibilidade, compatibilidade e usabilidade.

## Suposições (Assumptions)

- O usuário informa a cidade pela busca; localização automática não é requisito confirmado.
- A aplicação apresenta dados de uma cidade selecionada por vez.
- Celsius e Fahrenheit se aplicam a todas as temperaturas exibidas.
- A previsão abrange cinco dias, mas a inclusão do dia atual ainda depende de confirmação.
- Os dados meteorológicos serão obtidos de um serviço externo.
- A interface será responsiva e utilizável em navegadores móveis modernos.