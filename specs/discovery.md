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

- A fonte de dados pode não cobrir todas as cidades ou pode retornar resultados ambíguos para nomes iguais.
- Indisponibilidade, atraso ou limites de uso do serviço meteorológico podem impedir ou degradar as consultas.
- O briefing não define se “cinco dias” inclui o dia atual nem o nível de detalhe esperado para cada dia.
- Diferenças de fuso horário podem afetar a apresentação do clima atual e dos períodos da previsão.
- Metas de desempenho, disponibilidade, validade dos dados e a matriz de compatibilidade ainda precisam ser validadas com o provedor e com as expectativas do produto.

## Perguntas em Aberto (Open Questions)

- Qual serviço ou API será usado para busca de cidades e dados meteorológicos?
- A busca deve aceitar apenas nomes de cidades ou também país, estado, código postal ou localização do dispositivo?
- Como o usuário deve distinguir cidades com o mesmo nome?
- A previsão de cinco dias deve ser diária, horária ou incluir ambos os formatos?
- “Cinco dias” inclui o dia atual ou corresponde aos cinco dias seguintes?
- Quais informações devem aparecer no clima atual e na previsão, além da temperatura e da condição do tempo?
- A unidade escolhida deve ser mantida entre sessões ou dispositivos?
- A aplicação deve solicitar acesso à localização do usuário?
- Quais navegadores, dispositivos e versões devem compor a matriz de compatibilidade?
- As metas propostas de desempenho (95% em até 2 segundos) e disponibilidade (99,5% ao mês) são adequadas? Como medir e contabilizar dependências externas?
- Qual é o tempo máximo aceitável de validade dos dados meteorológicos e como o provedor informa a hora da última atualização?
- Quais critérios de avaliação de usabilidade devem ser adotados?

## Suposições (Assumptions)

- O usuário informa a cidade pela busca; localização automática não é requisito confirmado.
- A aplicação apresenta dados de uma cidade selecionada por vez.
- Celsius e Fahrenheit se aplicam a todas as temperaturas exibidas.
- A previsão abrange cinco dias, mas a inclusão do dia atual ainda depende de confirmação.
- Os dados meteorológicos serão obtidos de um serviço externo.
- A interface será responsiva e utilizável em navegadores móveis modernos.