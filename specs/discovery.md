## Contexto

A empresa precisa de uma aplicação de previsão do tempo que permita aos usuários consultar as condições meteorológicas de cidades de interesse. A experiência deve funcionar em dispositivos móveis e apresentar tanto o clima atual quanto a previsão para os próximos cinco dias.

O discovery considera como escopo inicial a busca de cidades, a consulta das informações meteorológicas e a alternância entre Celsius e Fahrenheit. A fonte dos dados, a cobertura geográfica e outros detalhes de produto ainda precisam ser definidos.

## Requisitos Funcionais

- **RF-01 — Buscar cidades:** o usuário deve poder pesquisar uma cidade pelo nome e selecionar o local desejado entre os resultados.
- **RF-02 — Consultar clima atual:** para a cidade selecionada, a aplicação deve exibir as condições meteorológicas atuais.
- **RF-03 — Consultar previsão:** a aplicação deve exibir a previsão para os próximos cinco dias da cidade selecionada.
- **RF-04 — Alternar unidade de temperatura:** o usuário deve poder alternar entre Celsius e Fahrenheit; as temperaturas apresentadas devem refletir a unidade escolhida.
- **RF-05 — Usar em dispositivos móveis:** as funcionalidades de busca e consulta devem estar disponíveis em dispositivos móveis.

## Requisitos Não-Funcionais

- **RNF-01 — Responsividade:** conteúdo e controles devem se adaptar a diferentes tamanhos de tela, sem impedir a consulta das informações em dispositivos móveis.
- **RNF-02 — Usabilidade:** a busca, a seleção da cidade e a alternância de unidade devem ser compreensíveis e acessíveis por interação direta.
- **RNF-03 — Clareza dos dados:** informações atuais e previsões devem estar associadas claramente à cidade e ao período correspondentes.
- **RNF-04 — Confiabilidade dos dados:** a aplicação deve informar quando os dados meteorológicos não estiverem disponíveis ou não puderem ser atualizados.
- **RNF-05 — Desempenho:** buscas e consultas devem responder em tempo adequado para uma experiência fluida. O limite aceitável precisa ser definido.

## Riscos

- A fonte de dados pode não cobrir todas as cidades ou pode retornar resultados ambíguos para nomes iguais.
- Indisponibilidade, atraso ou limites de uso do serviço meteorológico podem impedir ou degradar as consultas.
- O briefing não define se “cinco dias” inclui o dia atual nem o nível de detalhe esperado para cada dia.
- Diferenças de fuso horário podem afetar a apresentação do clima atual e dos períodos da previsão.
- Sem critérios de acessibilidade e suporte a navegadores definidos, a experiência pode variar entre usuários e dispositivos.

## Perguntas em Aberto (Open Questions)

- Qual serviço ou API será usado para busca de cidades e dados meteorológicos?
- A busca deve aceitar apenas nomes de cidades ou também país, estado, código postal ou localização do dispositivo?
- Como o usuário deve distinguir cidades com o mesmo nome?
- A previsão de cinco dias deve ser diária, horária ou incluir ambos os formatos?
- “Cinco dias” inclui o dia atual ou corresponde aos cinco dias seguintes?
- Quais informações devem aparecer no clima atual e na previsão, além da temperatura e da condição do tempo?
- A unidade escolhida deve ser mantida entre sessões ou dispositivos?
- A aplicação deve solicitar acesso à localização do usuário?
- Quais navegadores, dispositivos e padrões de acessibilidade devem ser suportados?
- Quais são os objetivos mensuráveis de desempenho, disponibilidade e atualização dos dados?

## Suposições (Assumptions)

- O usuário informa a cidade pela busca; localização automática não é requisito confirmado.
- A aplicação apresenta dados de uma cidade selecionada por vez.
- Celsius e Fahrenheit se aplicam a todas as temperaturas exibidas.
- A previsão abrange cinco dias, mas a inclusão do dia atual ainda depende de confirmação.
- Os dados meteorológicos serão obtidos de um serviço externo.
- A interface será responsiva e utilizável em navegadores móveis modernos.