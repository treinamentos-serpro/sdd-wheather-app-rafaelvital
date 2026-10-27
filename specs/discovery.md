## Contexto

A empresa precisa de uma aplicação de previsão do tempo que permita aos usuários consultar as condições meteorológicas de cidades de interesse. A experiência deve funcionar em dispositivos móveis e apresentar tanto o clima atual quanto a previsão diária para hoje e os quatro dias seguintes no fuso local da cidade.

O MVP considera busca manual de cidades no Brasil, consulta do clima atual e previsão em períodos diários, além da alternância entre Celsius e Fahrenheit. A fonte dos dados foi definida como Open-Meteo.

## Requisitos Funcionais

- **RF-01 — Buscar cidades:** o usuário deve poder pesquisar uma cidade pelo nome e selecionar o local desejado entre os resultados.
- **RF-02 — Consultar clima atual:** para a cidade selecionada, a aplicação deve exibir as condições meteorológicas atuais.
- **RF-03 — Consultar previsão:** a aplicação deve exibir a previsão diária para hoje e os quatro dias seguintes, segundo o calendário e o fuso horário local da cidade selecionada. O primeiro dia pode ser parcial.
- **RF-04 — Alternar unidade de temperatura:** o usuário deve poder alternar entre Celsius e Fahrenheit; as temperaturas apresentadas devem refletir a unidade escolhida.
- **RF-05 — Consultar em dispositivos móveis:** o usuário deve poder buscar cidades, selecionar um local, consultar o clima atual e visualizar a previsão em um dispositivo móvel.
- **RF-06 — Informar falhas de consulta:** quando os dados meteorológicos não estiverem disponíveis ou não puderem ser atualizados, a aplicação deve informar a situação ao usuário e não apresentar dados desatualizados como atuais.

## Requisitos Não-Funcionais

- **RNF-01 — Responsividade:** entre 320 px e 1440 px, o conteúdo e os controles devem se adaptar sem rolagem horizontal nem perda de funcionalidades. Validar em 320, 375, 768, 1024 e 1440 px, em retrato e paisagem, incluindo zoom de 200%.
- **RNF-02 — Usabilidade:** busca, seleção de cidade e alternância de unidade devem ter rótulos e estados compreensíveis. No MVP, a equipe fará uma validação interna por roteiro, registrando participantes, tarefas, conclusão sem ajuda e problemas encontrados; não haverá teste com usuários externos, e o resultado não será apresentado como evidência de usabilidade com o público geral.
- **RNF-03 — Clareza dos dados:** cada conjunto de condições atuais e previsão deve identificar claramente a cidade e o período correspondente. Datas e horários seguem os padrões pt-BR, usam relógio de 24 horas e identificam o fuso horário local da cidade consultada.
- **RNF-04 — Atualidade dos dados:** distinguir o horário de observação informado pela fonte do horário em que o app obteve os dados e da última tentativa de atualização. Considerar o clima atual válido por até 1 hora desde a observação e a previsão válida por até 6 horas desde a obtenção bem-sucedida. Em falhas, dados vencidos podem permanecer visíveis com aviso explícito e horário; nunca devem ser apresentados como atuais. Uma falha parcial deve afetar apenas a seção que falhou.
- **RNF-05 — Desempenho:** em uma janela de 7 dias, o percentil 95 das buscas deve apresentar resultados em até 2 segundos, medidos ponta a ponta no navegador desde o envio da busca até os resultados ficarem visíveis, incluindo o provedor. O protocolo de medição deve registrar modelo do dispositivo, sistema operacional, versão do navegador, região, rede, quantidade de amostras e política de cache; usar celular de faixa intermediária e conexão 4G no Brasil.
- **RNF-06 — Acessibilidade:** atender ao nível AA da WCAG 2.2. A evidência incluirá auditoria automática e testes manuais de teclado, foco e leitor de tela; violações aplicáveis devem ser corrigidas.
- **RNF-07 — Disponibilidade:** 99,5% ao mês permanece como objetivo futuro, sem meta mensurável no MVP. Durante falhas externas, o app deve cumprir o RF-06.
- **RNF-08 — Compatibilidade:** suportar as versões estável atual e imediatamente anterior de Chrome, Edge, Firefox e Safari nos sistemas operacionais desktop e móveis definidos na matriz de compatibilidade da especificação.

## Riscos

As probabilidades abaixo são estimativas qualitativas para o discovery; devem ser revistas após as validações do provedor e a definição dos critérios de aceite na especificação.

| Categoria | Risco | Probabilidade | Impacto | Estratégia de mitigação |
|---|---|---|---|---|
| Técnico | Indisponibilidade, lentidão ou limites do provedor de dados impedirem consultas ou degradarem a experiência. | Média | Alto: usuários podem ficar sem clima atual ou previsão. | Verificar cobertura, limites e frequência de atualização do Open-Meteo; definir timeouts e tratamento de falhas; informar a indisponibilidade e o horário da última atualização sem apresentar dados antigos como atuais. |
| Técnico / Produto | Dados desatualizados ou períodos interpretados incorretamente por causa do fuso horário da cidade. | Média | Alto: informações podem parecer atuais ou corresponder ao dia errado. | Definir a validade dos dados e usar o fuso local da cidade; mostrar o horário da última atualização e o período da previsão; testar viradas de data e dados vencidos. |
| Produto | O primeiro dia da previsão é parcial e a virada de data depende do fuso local da cidade. | Média | Médio: usuários podem interpretar incorretamente a data ou comparar períodos de durações diferentes. | Identificar cada dia pela data local, indicar que o primeiro dia pode ser parcial e validar a apresentação em diferentes fusos e horários de consulta. |
| Técnico / Produto | Resultados de cidades homônimas ou cobertura geográfica insuficiente levarem à seleção do local errado. | Média | Alto: o usuário recebe informações meteorológicas de outra cidade. | Definir as regiões atendidas e exibir país e estado/região nos resultados; estabelecer regras para ordenação, ausência e excesso de resultados. |
| Técnico | A busca pode não cumprir a meta P95 de até 2 segundos, medida ponta a ponta incluindo o provedor. | Média | Médio/alto: a experiência pode parecer lenta, especialmente em redes móveis. | Medir no ambiente definido no RNF-05; otimizar o carregamento e evitar consultas desnecessárias enquanto o usuário digita. |
| Técnico / Produto | A interface pode falhar em telas, orientações, navegadores ou tecnologias assistivas não contemplados nos testes. | Média | Alto: alguns usuários podem não conseguir concluir tarefas. | Definir a matriz de dispositivos e navegadores; testar as larguras e orientações previstas; validar teclado, foco, contraste e nomes acessíveis em relação à WCAG 2.2 nível AA. |
| Produto | O indicador de sucesso do MVP é qualitativo e funcionalidades fora do escopo podem gerar expectativas adicionais. | Média | Alto: prioridades podem mudar durante o desenvolvimento e o produto pode não atender à necessidade principal. | Manter explícitos o público, o escopo do MVP e o indicador qualitativo; definir métricas quantitativas se forem necessárias para avaliar o lançamento. |
| Técnico / Produto | A disponibilidade não será medida no MVP e a revisão de usabilidade será interna. | Média | Médio/alto: não será possível comprovar objetivamente disponibilidade mensal nem usabilidade com usuários reais. | Tratar 99,5% como objetivo futuro e planejar monitoramento e testes com usuários caso esses critérios se tornem requisitos de lançamento. |

## Perguntas levantadas no discovery

As perguntas abaixo registram os pontos investigados originalmente. As respostas e decisões da entrevista estão consolidadas na seção **Decisões**.

Os impactos descrevem os riscos identificados antes das decisões.

### Produto e escopo

1. **Decidida:** público geral em consultas ocasionais.<br>
   **Impacto:** sem conhecer usuários e contexto de uso, fica difícil priorizar informações, linguagem, acessibilidade e decisões como localização automática.

2. **Decidida:** uma cidade por vez; favoritos ficam fora do MVP e a última cidade será lembrada localmente.<br>
   **Impacto:** a resposta muda navegação, persistência de dados, interface e escopo de implementação. O briefing assume uma cidade por vez, mas não confirma se isso limita a experiência.

3. **Decidida:** localização automática, favoritos, previsão horária e alertas ficam fora do MVP.<br>
   **Impacto:** sem limites claros, itens como alertas meteorológicos, localização automática, favoritos e previsão horária podem surgir como expectativas durante a implementação.

4. **Parcialmente decidida:** o objetivo qualitativo é permitir consultar diferentes cidades e ver seu clima; o MVP terá validação interna por tarefas, sem teste com usuários externos.<br>
   **Impacto:** sem uma medida de sucesso, não será possível avaliar se o produto atende à necessidade, além de cumprir requisitos técnicos.

### Busca e seleção de cidades

5. **Decidida:** busca manual pelo nome da cidade; não solicitar localização do dispositivo.<br>
   **Impacto:** define o comportamento esperado, o provedor necessário e como o usuário encontrará locais fora do seu país.

6. **Decidida:** cobertura pretendida no Brasil, sujeita à validação do provedor.<br>
   **Impacto:** cobertura geográfica, idiomas de busca, formatos de endereço e qualidade dos resultados podem variar por região.

7. **Decidida:** exibir cidade, estado/região e país para desambiguar resultados.<br>
   **Impacto:** sem contexto suficiente, o usuário pode selecionar a cidade errada e receber dados incorretos para sua intenção.

8. **Decidida:** enviar por Enter ou botão a partir de dois caracteres; a busca não é automática durante a digitação.<br>
   **Impacto:** sem essas definições, a experiência pode ser inconsistente e gerar consultas desnecessárias ou resultados confusos.

9. **Decidida:** ordenar por relevância, limitar a dez resultados e orientar a refinar a consulta quando não houver resultados.<br>
   **Impacto:** faltam estados de vazio, orientação para refinar a busca e regras de ordenação ou paginação.

10. **Decidida:** o app não solicita permissão de localização.<br>
    **Impacto:** afeta privacidade, permissões, fluxo inicial e experiência de quem não concede acesso.

### Dados meteorológicos e apresentação

11. **Decidida:** será usado o Open-Meteo, sem API key.<br>
    **Justificativa e impacto:** a fonte fica definida sem exigir configuração de chave de API. Cobertura de cidades, frequência de atualização e demais limitações do provedor ainda precisam ser verificadas.

12. **Decidida:** exibir temperatura, condição textual, sensação térmica, umidade, vento e precipitação; a validade da observação é definida no RNF-04.<br>
    **Impacto:** sem definição, produto e implementação podem discordar sobre o conteúdo mínimo e sobre quando o dado está atualizado.

13. **Decidida:** exibir temperaturas mínima/máxima, condição, probabilidade/volume de chuva e vento.<br>
    **Impacto:** afeta o layout, a legibilidade em dispositivos móveis e a possibilidade de comparar os dias.

14. **Decidida:** a previsão cobre hoje e os quatro dias seguintes no calendário local; o primeiro dia pode ser parcial.<br>
    **Justificativa e impacto:** harmoniza a previsão diária com a interpretação de dias do calendário e com o fuso da cidade.

15. **Decidida:** a previsão será apenas diária no MVP.<br>
    **Impacto:** muda a quantidade de informação, o design, os dados exigidos do provedor e o significado prático da previsão.

16. **Decidida:** cada período corresponde a um dia do calendário local da cidade; a previsão começa no dia local da consulta e inclui os quatro dias seguintes. O primeiro dia pode ser parcial.<br>
    **Impacto:** pode haver divergência entre as datas exibidas e os períodos que o usuário considera como “hoje” e “amanhã”.

17. **Decidida:** interface e formatos regionais em pt-BR, com relógio de 24 horas.<br>
    **Impacto:** o briefing não define idioma, formatação regional nem se haverá suporte a vários idiomas; isso pode tornar rótulos e datas ambíguos.

18. **Decidida:** usar e identificar o fuso horário local da cidade consultada.<br>
    **Impacto:** uma escolha inconsistente pode deslocar a previsão em relação ao local, mesmo que o fuso seja indicado na interface.

19. **Decidida:** temperatura em Celsius por padrão, com alternância para Fahrenheit; vento em km/h e precipitação em mm.<br>
    **Impacto:** alternar Celsius/Fahrenheit não esclarece unidades para vento, precipitação ou distância. Misturar padrões pode confundir usuários de diferentes regiões.

20. **Decidida:** exibir ícones e descrições textuais das condições.<br>
    **Impacto:** sem essa definição, a compreensão pode depender de símbolos ambíguos ou informações que não sejam acessíveis para todos.

21. **Decidida:** alertas meteorológicos, avisos de tempo severo e recomendações ficam fora do MVP.<br>
    **Impacto:** a decisão pode afetar dados, apresentação e expectativas de segurança do usuário.

### Atualização, falhas e persistência

22. **Decidida:** clima atual válido por até 1 hora desde a observação; previsão válida por até 6 horas desde a obtenção bem-sucedida.<br>
    **Impacto:** a regra depende do provedor e precisa distinguir clima atual de previsão; sem ela, o app não sabe quando sinalizar que os dados podem estar desatualizados.

23. **Decidida:** dados vencidos podem ser exibidos com horário e aviso explícito, nunca como atuais.<br>
    **Impacto:** a exigência de não rotular dados antigos como atuais não determina o que o usuário verá durante uma falha, podendo levar a comportamentos incompatíveis entre telas.

24. **Decidida:** apresentar as seções que carregaram, indicar a seção que falhou e oferecer nova tentativa; estados de carregamento e indisponibilidade devem ser explícitos.<br>
    **Impacto:** o RF-06 prevê informar falhas, mas não define mensagens, ações de recuperação, apresentação de dados parcialmente disponíveis ou comportamento offline.

25. **Decidida:** atualização automática a cada 30 minutos e atualização manual disponível.<br>
    **Impacto:** sem regras de atualização, podem ocorrer consultas excessivas, consumo desnecessário de recursos ou dados menos recentes do que o esperado.

26. **Decidida:** lembrar localmente a última cidade e a unidade; não haverá contas nem sincronização entre dispositivos.<br>
    **Impacto:** a persistência da unidade está em aberto e a da cidade não foi especificada. Sem decisão, o app pode perder preferências ou criar expectativas incompatíveis sobre contas e sincronização.

27. **Parcialmente decidida:** exibir atribuição na seção Sobre/Créditos; validar limites e condições de uso do provedor antes do lançamento.<br>
    **Impacto:** a escolha de fonte pode exigir créditos visíveis, limitar cache ou afetar a viabilidade legal e operacional da experiência.

### Qualidade, critérios e operação

28. **Parcialmente decidida:** a equipe fará uma validação interna por roteiro, sem teste com usuários externos. Definir na especificação os participantes, o roteiro e o registro de conclusão/problemas antes de implementar.<br>
    **Impacto:** “sem instruções externas” é uma intenção, mas não permite verificar objetivamente se o requisito foi atendido.

29. **Parcialmente decidida:** manter P95 ≤ 2 segundos, ponta a ponta, incluindo o provedor, em janela de sete dias, celular de faixa intermediária e 4G no Brasil. Especificar modelo, sistema operacional, navegador, amostra e política de cache no protocolo de teste.<br>
    **Impacto:** excluir demora do provedor não define como separar esse tempo do restante nem permite comparar resultados de forma consistente. A métrica pode parecer cumprida sem refletir a experiência real.

30. **Decidida para o MVP:** 99,5% mensal é objetivo futuro, sem meta mensurável agora; falhas externas devem seguir o RF-06.<br>
    **Impacto:** sem método de medição e exclusões acordados, não há como verificar a meta nem distinguir indisponibilidade do app de degradação externa.

31. **Parcialmente decidida:** suportar versões estável atual e imediatamente anterior de Chrome, Edge, Firefox e Safari. Fechar na especificação os sistemas operacionais e a matriz de teste desktop/mobile.<br>
    **Impacto:** “dispositivos móveis e desktop” não define uma matriz testável; a cobertura pode variar entre equipes e deixar usuários sem suporte.

32. **Decidida:** validar 320, 375, 768, 1024 e 1440 px, em retrato e paisagem, incluindo zoom de 200%, sem rolagem horizontal ou perda de funcionalidades.<br>
    **Impacto:** os requisitos atuais deixam dimensões e condições de teste indefinidas, apesar de exigirem ausência de rolagem horizontal e perda de funcionalidades.

33. **Parcialmente decidida:** combinar auditoria automática e testes manuais de teclado, foco e leitor de tela; detalhar a ferramenta e o roteiro na especificação e corrigir violações aplicáveis.<br>
    **Impacto:** listar teclado, foco e contraste não cobre, por si só, todos os critérios aplicáveis; sem método de verificação, “AA” pode ser interpretado de formas diferentes.

**Pendências para fechar na especificação:** definir o roteiro e os participantes da validação interna; registrar o modelo de dispositivo, sistemas operacionais, navegador/versão, tamanho da amostra e política de cache do ensaio de desempenho; e fechar a matriz de compatibilidade desktop/mobile.

**Validações antes do lançamento:** confirmar cobertura no Brasil, disponibilidade dos campos necessários para clima atual e agregados diários, frequência de atualização, limites, condições de uso e atribuição do Open-Meteo. Validar que os dados da fonte permitem apresentar hoje e os quatro dias seguintes no fuso local da cidade. Executar as verificações de desempenho, compatibilidade e acessibilidade definidas na especificação.

## Decisões

- **Produto e escopo (perguntas 1–4):** público geral em consultas ocasionais; uma cidade por vez. O MVP inclui busca manual, clima atual e previsão diária; localização automática, favoritos, previsão horária e alertas ficam fora do escopo. A cidade e a unidade serão lembradas localmente, sem contas ou sincronização entre dispositivos. O objetivo do produto é permitir consultar diferentes cidades e ver seu clima; não foi definido KPI de negócio. A validação interna de usabilidade está descrita no RNF-02.
- **Busca (perguntas 5–10):** aceitar nome de cidade brasileira. Exibir cidade, estado/região e país para desambiguar; se o provedor não retornar algum desses dados, exibir a localização disponível sem inventar informação. A busca é enviada por Enter ou botão, a partir de dois caracteres; ordenar por relevância, limitar a dez resultados e orientar o usuário a refinar quando não houver resultados. Não solicitar localização do dispositivo.
- **Fonte de dados (perguntas 11 e 27):** Open-Meteo, sem API key. Exibir atribuição na seção Sobre/Créditos. Antes do lançamento, validar cobertura, limites e condições de uso; limitar o cache local às validades definidas nesta especificação.
- **Clima e previsão (perguntas 12–16):** clima atual com temperatura, condição textual, sensação térmica, umidade, vento e precipitação. Exibir ícone e descrição textual. A previsão terá somente visualização diária para hoje e os quatro dias seguintes no calendário local da cidade, com temperaturas mínima/máxima, condição, probabilidade/volume de chuva e vento. O primeiro dia pode ser parcial.
- **Idioma, horários e unidades (perguntas 17–19):** interface em pt-BR; datas e números em padrões pt-BR e relógio de 24 horas. Usar o fuso local da cidade consultada e identificá-lo. Celsius é a unidade padrão de temperatura, com alternância para Fahrenheit; vento em km/h e precipitação em mm.
- **Alertas (pergunta 21):** alertas meteorológicos, avisos de tempo severo e recomendações ficam fora do MVP.
- **Atualização e falhas (perguntas 22–25):** validade de até 1 hora para clima atual, contada do horário de observação fornecido pela fonte, e 6 horas para previsão, contadas da obtenção bem-sucedida pelo app. Se uma atualização falhar, exibir dados vencidos com horário e aviso, nunca como atuais; manter independentes as seções que carregaram e as que falharam, com ação para tentar novamente. Atualizar automaticamente a cada 30 minutos e oferecer atualização manual.
- **Usabilidade e desempenho (perguntas 28–29):** não haverá testes com usuários externos no MVP. A equipe fará validação interna por roteiro, registrando participantes, tarefas, conclusão sem ajuda e problemas encontrados. Medir a busca ponta a ponta, incluindo o provedor, por 7 dias; meta P95 de até 2 segundos, do envio até os resultados visíveis. O protocolo deve registrar modelo do dispositivo, sistema operacional, navegador e versão, rede, região, amostra e política de cache.
- **Disponibilidade, compatibilidade e acessibilidade (perguntas 30–33):** 99,5% mensal é objetivo futuro, sem meta mensurável no MVP. Suportar as versões estável atual e imediatamente anterior de Chrome, Edge, Firefox e Safari; a especificação deve registrar os sistemas operacionais e a matriz de testes desktop/mobile. Validar os tamanhos e orientações especificados no RNF-01. Para WCAG 2.2 AA, combinar auditoria automática com testes manuais de teclado, foco e leitor de tela, registrar a evidência e corrigir violações aplicáveis.

## Suposições (Assumptions)

- O usuário informa a cidade pela busca; localização automática não é requisito confirmado.
- A aplicação apresenta dados de uma cidade selecionada por vez e lembra localmente a última cidade consultada e a unidade escolhida.
- Celsius e Fahrenheit se aplicam a todas as temperaturas exibidas.
- A previsão abrange hoje e os quatro dias seguintes no calendário local da cidade selecionada; o primeiro dia pode ser parcial.
- Os dados meteorológicos serão obtidos do Open-Meteo.
- A interface será em pt-BR e suportará as versões estável atual e imediatamente anterior de Chrome, Edge, Firefox e Safari, nos sistemas operacionais definidos na matriz de compatibilidade da especificação.