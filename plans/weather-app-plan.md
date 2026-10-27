# Weather App — Technical Plan

## Architecture

Aplicação cliente React, organizada em três camadas simples:

1. **UI (`src/components/`)** apresenta busca, seleção de cidade, clima atual, previsão e estados acessíveis.
2. **Hooks (`src/hooks/`)** coordenam estado da interface, consultas e atualização periódica.
3. **Services (`src/services/`)** fazem chamadas HTTP, validam respostas externas e convertem os dados para os contratos da aplicação.

Os modelos e contratos compartilhados ficam em `src/types/`. Funções puras de validação, unidade, condição meteorológica e formatação ficam em `src/utils/`.

A busca de geocodificação é independente das consultas meteorológicas. Ao selecionar uma cidade, clima atual e previsão são carregados em consultas separadas, podendo falhar e ser atualizados independentemente. Uma resposta só pode alterar a interface se ainda corresponder à busca ou cidade selecionada mais recente.

Decisões relacionadas: **FR-01–FR-08**, **AC-01–AC-08**, **NFR-04** e **NFR-10**.

## Tech Stack

- **TypeScript strict, React e Vite:** stack do projeto; contratos tipados para dados externos e estado.
- **Tailwind CSS:** tema dark glassmorphism do projeto, layout mobile-first e responsividade entre os viewports definidos em NFR-01. A previsão usa grid responsivo de cinco dias (`grid-cols-2 sm:grid-cols-3 lg:grid-cols-5`).
- **Localização da interface:** textos, datas e números apresentados seguem pt-BR; identificadores de código e termos técnicos seguem en-US.
- **Fetch nativo:** suficiente para as chamadas HTTP; evita dependência adicional.
- **Vitest e Testing Library:** testes unitários e de componentes.
- **Playwright:** testes dos fluxos completos e viewports definidos pela spec.
- **Biome:** lint e formatação, conforme convenções do projeto.
- **`localStorage`:** persistir somente unidade e dados mínimos para restaurar a última cidade; nenhuma previsão ou resposta meteorológica é persistida.

Não introduzir biblioteca de estado global, cache de dados ou camada genérica de API no MVP: os dados e fluxos cabem em hooks e services específicos.

## Project Structure

```text
src/
  components/
    CitySearch.tsx
    SearchResults.tsx
    CurrentWeather.tsx
    DailyForecast.tsx
    TemperatureUnitToggle.tsx
    WeatherStatus.tsx
    Credits.tsx
  hooks/
    useCitySearch.ts
    useWeather.ts
    usePreferences.ts
  services/
    geocodingService.ts
    weatherService.ts
  types/
    city.ts
    weather.ts
  utils/
    weatherValidation.ts
    weatherCode.ts
    temperature.ts
    dateTime.ts
tests/
  unit/
  e2e/
```

Os nomes indicam responsabilidades planejadas, não exigem criar todos os arquivos como abstrações independentes. Os testes devem seguir a estrutura já adotada no repositório, se houver convenção existente.

## Data Model

Tipos abaixo descrevem contratos, não implementação. Os campos opcionais representam dados que podem faltar na resposta do provedor; não devem ser preenchidos com valores inventados.

```ts
type TemperatureUnit = "celsius" | "fahrenheit";

interface City {
  name?: string;
  latitude?: number;
  longitude?: number;
  country?: string;
  countryCode?: string;
  region?: string;
  providerId?: number;
}

interface SelectableCity extends City {
  name: string;
  latitude: number;
  longitude: number;
}

interface CurrentWeather {
  temperatureC?: number;
  observedAt?: string; // instante parseável, normalizado para ISO UTC
  apparentTemperatureC?: number;
  humidityPercent?: number;
  precipitationMm?: number;
  weatherCode?: number;
  windSpeedKmh?: number;
}

interface ForecastDay {
  date: string; // data local da cidade, formato YYYY-MM-DD
  status: "complete" | "incomplete";
  minimumC?: number;
  maximumC?: number;
  weatherCode?: number;
  precipitationProbabilityPercent?: number;
  precipitationMm?: number;
  maximumWindKmh?: number;
}

interface WeatherData {
  city: City;
  timezone: string;
  current?: CurrentWeather;
  forecast?: ForecastDay[];
}

type RequestStatus =
  | "idle"
  | "loading"
  | "success"
  | "error"
  | "empty"
  | "unavailable";

type WeatherSectionStatus =
  | RequestStatus
  | "incomplete";

interface SectionState<T> {
  status: WeatherSectionStatus;
  city: City;
  data?: T;
  lastSuccessAt?: string; // ISO UTC, quando aplicável
  lastAttemptAt?: string; // ISO UTC
  errorMessage?: string;
  isStale?: boolean;
}

interface SavedPreferences {
  temperatureUnit: TemperatureUnit;
  lastCity?: City;
}
```

Uma cidade só é selecionável quando tem nome e coordenadas finitas dentro dos limites geográficos. Clima atual completo exige temperatura e horário válido; cada dia completo exige data e temperaturas mínima e máxima válidas. Dias sem os dados essenciais recebem `status: "incomplete"` e não exibem valores inventados. A condição e o ícone são derivados de códigos WMO conhecidos, nunca inferidos quando o código falta ou é desconhecido.

## Data Flow

1. A pessoa envia uma consulta aparada pelo botão ou por Enter. Comprimento aceito: 2–100 pontos de código Unicode. Não há busca durante a digitação.
2. O serviço de geocodificação solicita até dez resultados no Brasil. Antes de exibir, a aplicação descarta itens cujo `countryCode` indique país diferente de `BR`; mantém a ordem do provedor e exibe somente campos recebidos.
3. Resultados sem nome ou coordenadas válidas podem ser exibidos com indicação de indisponibilidade, mas somente resultados brasileiros com nome e coordenadas válidas podem ser selecionados.
4. Ao selecionar uma cidade, a aplicação associa a seleção às novas consultas e inicia, em paralelo, as chamadas de clima atual e previsão. Dados da cidade anterior não podem ser identificados como dados da nova seleção.
5. Cada service valida os dados e produz os tipos normalizados. A UI apresenta campos válidos, sinaliza dados essenciais incompletos e mantém independentes os estados das duas seções.
6. A unidade escolhida é aplicada na apresentação; valores meteorológicos permanecem em Celsius no modelo.
7. Atualizações automáticas, manuais e ao retornar à página verificam elegibilidade por seção. Resultados de buscas ou cidades anteriores não atualizam o estado atual.

## External APIs

As respostas são externas e devem ser validadas antes do mapeamento. Campos relevantes ausentes ou inválidos permanecem ausentes no modelo; não devem ser substituídos por zero ou por dados inferidos.

### Geocoding — busca de cidades

- Endpoint: `https://geocoding-api.open-meteo.com/v1/search`
- Parâmetros: `name` com o texto aparado, `countryCode=BR`, `count=10`, `language=pt`. Codificar a consulta como parâmetro URL sem alterar seus caracteres.
- Exemplo de URL:
  `https://geocoding-api.open-meteo.com/v1/search?name=S%C3%A3o%20Paulo&countryCode=BR&count=10&language=pt`
- Exemplo resumido de resposta:

  ```json
  {
    "results": [
      {
        "id": 3448439,
        "name": "São Paulo",
        "latitude": -23.5475,
        "longitude": -46.63611,
        "country": "Brasil",
        "country_code": "BR",
        "admin1": "São Paulo"
      }
    ]
  }
  ```

- Mapeamento de cada item de `results` para `City`: `id` → `providerId`, `name` → `name`, `latitude`/`longitude` → coordenadas, `country` → `country` e `country_code` → `countryCode`, `admin1` → `region`. Descartar resultados com `countryCode` diferente de `BR`, mesmo se retornados pelo provedor. Para os demais, manter opcionais os campos ausentes e preservar a ordem recebida. Resultados sem nome ou coordenadas válidas podem ser mostrados com indicação de indisponibilidade, mas não selecionados para consulta meteorológica.
- Timeout: **8 segundos**.

### Forecast — clima atual e previsão diária

- Endpoint: `https://api.open-meteo.com/v1/forecast`
- Usar latitude e longitude da cidade selecionada. Para suportar falhas e novas tentativas independentes, fazer uma solicitação de clima atual e outra de previsão.
- Parâmetros comuns: `latitude`, `longitude`, `timezone=auto`, `temperature_unit=celsius`, `wind_speed_unit=kmh` e `precipitation_unit=mm`. Solicitar também `forecast_days=5` na consulta diária.
- Consulta de clima atual: parâmetro `current=temperature_2m,apparent_temperature,relative_humidity_2m,precipitation,weather_code,wind_speed_10m`.
- Consulta de previsão: parâmetro `daily=temperature_2m_min,temperature_2m_max,weather_code,precipitation_probability_max,precipitation_sum,wind_speed_10m_max`.
- Exemplos de URL:
  - Atual:
    `https://api.open-meteo.com/v1/forecast?latitude=-23.5475&longitude=-46.63611&current=temperature_2m,apparent_temperature,relative_humidity_2m,precipitation,weather_code,wind_speed_10m&timezone=auto&temperature_unit=celsius&wind_speed_unit=kmh&precipitation_unit=mm`
  - Previsão:
    `https://api.open-meteo.com/v1/forecast?latitude=-23.5475&longitude=-46.63611&daily=temperature_2m_min,temperature_2m_max,weather_code,precipitation_probability_max,precipitation_sum,wind_speed_10m_max&timezone=auto&forecast_days=5&temperature_unit=celsius&wind_speed_unit=kmh&precipitation_unit=mm`
- Exemplo resumido de resposta atual:

  ```json
  {
    "timezone": "America/Sao_Paulo",
    "utc_offset_seconds": -10800,
    "current": {
      "time": "2025-03-08T14:00",
      "temperature_2m": 25.4,
      "apparent_temperature": 26.1,
      "relative_humidity_2m": 68,
      "precipitation": 0.2,
      "weather_code": 3,
      "wind_speed_10m": 12.5
    }
  }
  ```

- Exemplo resumido de resposta diária:

  ```json
  {
    "timezone": "America/Sao_Paulo",
    "utc_offset_seconds": -10800,
    "daily": {
      "time": ["2025-03-08", "2025-03-09"],
      "temperature_2m_min": [19.2, 20.1],
      "temperature_2m_max": [27.8, 28.4],
      "weather_code": [3, 61],
      "precipitation_probability_max": [10, 70],
      "precipitation_sum": [0.0, 4.2],
      "wind_speed_10m_max": [18.3, 22.1]
    }
  }
  ```

- Mapeamento da resposta atual para `CurrentWeather`: `current.temperature_2m` → `temperatureC`, `current.time` → `observedAt`, `apparent_temperature` → `apparentTemperatureC`, `relative_humidity_2m` → `humidityPercent`, `precipitation` → `precipitationMm`, `weather_code` → `weatherCode` e `wind_speed_10m` → `windSpeedKmh`.
- Mapeamento da resposta diária para um `ForecastDay` por data local: `daily.time[i]` → `date`, `temperature_2m_min[i]` → `minimumC`, `temperature_2m_max[i]` → `maximumC`, `weather_code[i]` → `weatherCode`, `precipitation_probability_max[i]` → `precipitationProbabilityPercent`, `precipitation_sum[i]` → `precipitationMm` e `wind_speed_10m_max[i]` → `maximumWindKmh`. O campo `daily.time` contém datas locais do fuso retornado. Derivar a data de hoje no fuso IANA retornado, validar a sequência local de hoje e dos quatro dias seguintes e produzir exatamente essas cinco datas na interface. Associar os dados pela data, não presumir que um índice representa o dia esperado. Se uma data esperada estiver ausente, inválida ou sem mínima/máxima válidas, representá-la como `incomplete`, sem inventar valores; preservar os outros dias válidos. Ignorar datas fora do intervalo solicitado.
- Combinar as seções independentes em `WeatherData` com a cidade selecionada e o `timezone` retornado. Cada seção pode estar ausente enquanto carrega ou quando sua própria consulta falha; seu estado e seus horários de tentativa/sucesso permanecem em `SectionState`.
- Interpretar `current.time` usando o fuso retornado para obter um instante válido e exibi-lo com o fuso IANA e o offset aplicável. Registrar o horário de obtenção da previsão no app quando a resposta válida for recebida e processada; não confundir esse horário com `daily.time`.
- Timeout: **10 segundos** por solicitação.

Todas as chamadas usam HTTPS. Validar antes do lançamento a cobertura no Brasil, campos, frequência de atualização, limites e condições de uso. A interface Sobre/Créditos deve atribuir os dados meteorológicos ao Open-Meteo com link para o provedor.

## State Management

O estado vive nos hooks de cada fluxo, sem store global:

- `useCitySearch` mantém texto, resultados e status da busca (`idle`, `loading`, `success`, `empty`, `error` ou `unavailable`). Cada envio identifica sua requisição; somente a mais recente pode atualizar resultados.
- `useWeather` mantém cidade selecionada e estados separados para clima atual e previsão. Cada seção usa `idle`, `loading`, `success`, `error` ou `unavailable`, com `incomplete` para resposta recebida sem dados essenciais válidos. A seção diária também pode representar `empty` se não houver dias retornados.
- `usePreferences` mantém a unidade atual e persiste localmente a unidade e os dados mínimos para restaurar a última cidade.

Sucesso e dados são mantidos independentemente para cada seção: falha ou carregamento de uma não apaga uma seção válida já carregada. Se a atualização falhar, dados anteriores podem continuar visíveis apenas com indicação explícita de vencidos, cidade a que pertencem e seus horários; nunca podem aparecer como dados atuais. Ao selecionar outra cidade, os dados anteriores não podem ser associados à nova seleção.

Os valores meteorológicos no estado permanecem em Celsius, exatamente como recebidos e validados. A unidade escolhida é aplicada como valor derivado durante a renderização, sem alterar o estado meteorológico e sem novo request:

- Celsius: exibir o valor em Celsius, arredondado ao inteiro mais próximo.
- Fahrenheit: converter com `°F = °C × 9/5 + 32` e então arredondar ao inteiro mais próximo.
- Aplicar a mesma regra a temperatura atual, sensação térmica e mínimas/máximas diárias. Vento continua em km/h e precipitação em mm.

Ao abrir o app, restaurar somente preferências válidas e, se houver cidade salva, iniciar uma consulta nova. Não restaurar dados meteorológicos. Se `localStorage` estiver indisponível ou inválido, manter o app utilizável e usar Celsius por padrão.

Cada seção tem seu próprio intervalo de 30 minutos, contado do fim da última tentativa. Não iniciar chamadas simultâneas para a mesma seção e cidade. Ao retornar à página, atualizar imediatamente apenas as seções cujo intervalo venceu.

## Error Handling

- **Rede:** falha de conexão coloca apenas a requisição/seção correspondente em `unavailable`; manter seção independente e seus dados válidos. Oferecer nova tentativa.
- **API:** tratar status HTTP de erro, JSON inválido e estrutura inesperada como erro explícito, sem interpretar ausência de dados como sucesso vazio. Se a API retornar uma coleção válida vazia (busca sem cidades ou previsão sem dias), usar `empty` e orientar a pessoa conforme o contexto.
- **Timeout:** cancelar geocodificação após 8 segundos e cada consulta meteorológica após 10 segundos; finalizar em `unavailable` e permitir nova tentativa. Registrar o horário de conclusão da tentativa; o timeout de uma seção não bloqueia a outra.
- **Resposta parcial/incompleta:** validar cada campo; complementares inválidos são omitidos, sem zeros substitutos. Dados essenciais inválidos produzem `incomplete` para o clima atual ou para o dia afetado, preservando outros campos/seções/dias válidos. Código meteorológico ausente ou desconhecido não impede os demais dados.
- **Falha parcial:** controlar atual e previsão independentemente. Se uma consulta falhar, preservar a outra seção carregada e disponibilizar retry apenas para a seção afetada.
- **Dados vencidos:** atual válido até 60 minutos, inclusive, após observação; previsão válida até seis horas, inclusive, desde que a resposta válida foi recebida/processada. Dados futuros, inválidos ou vencidos não são marcados atuais. Em falha de atualização, dados anteriores podem permanecer visíveis com aviso, timestamp e cidade corretos.
- **Concorrência:** descartar respostas de buscas/cidades antigas; impedir requests simultâneos para mesma cidade e seção. Uma resposta anterior não pode substituir estado mais recente.
- Registrar obtenção válida da previsão e fim de cada tentativa em UTC, exibindo horários no fuso IANA da cidade e com offset identificável. Datas diárias usam o calendário local da cidade.
- Estados de carregamento, vazio, erro e incompletude devem ser anunciados por tecnologia assistiva. Ações de retry e demais controles precisam de nome acessível e operação por teclado.

## Testing Strategy

- **Funções puras (Vitest):** testar conversão Celsius/Fahrenheit e arredondamento após conversão; formatação de temperatura, umidade, precipitação e vento; validação de números, faixas, datas, coordenadas e mínima/máxima; validade temporal inclusiva e rejeição de horários futuros; formatação de data/hora no fuso IANA; mapeamento de códigos WMO conhecidos e desconhecidos. Incluir limites e entradas ausentes/inválidas.
- **Services (Vitest):** substituir `fetch` por mock e verificar URLs/parâmetros exatos, método, timeout/cancelamento, mapeamento da resposta, limite e ordem dos resultados. Cobrir sucesso, lista vazia, status HTTP de erro, JSON/estrutura inválidos, falha de rede, timeout e campos parciais. Verificar que atual e previsão podem falhar e ser repetidos independentemente.
- **Componentes (Vitest + Testing Library):** renderizar estados `idle`, `loading`, `success`, `error`, `unavailable`, `empty` e `incomplete` onde aplicáveis, incluindo sucesso parcial e dias ausentes/incompletos na sequência de cinco datas. Cobrir busca por Enter/botão, seleção, resultado estrangeiro filtrado, retry, unidade sem nova chamada e dados vencidos com aviso. Verificar nomes/roles acessíveis, anúncio de estados e operação por teclado.
- **Hooks e concorrência (Vitest):** controlar promises e relógio para confirmar descarte de resposta antiga, isolamento entre cidades/seções, prevenção de requests simultâneos, intervalos de 30 minutos, atualização ao retornar à página e ausência de consulta causada apenas pela troca de unidade.
- **E2E (Playwright):** percorrer busca de cidade, seleção e exibição de clima/previsão; alternar unidade; atualizar e tentar novamente; persistir/restaurar preferências iniciando consulta nova; confirmar que armazenamento local indisponível ou preferências inválidas não bloqueiam o uso; simular resposta fora de ordem, falha parcial, timeout, resultados vazios e resultado estrangeiro filtrado. Interceptar as chamadas externas para tornar os cenários determinísticos.
- **E2E responsivo:** validar o fluxo completo nos viewports de **320, 375, 768, 1024 e 1440 px**, em retrato e paisagem, incluindo zoom de 200% sobre viewport inicial de 1280 × 720 CSS px. Confirmar ausência de rolagem horizontal e acesso por rolagem vertical a controles essenciais, conforme NFR-01.
- Executar validação de compatibilidade nos navegadores/sistemas cobertos por NFR-08 e registrar versões e combinações não suportadas pelo fabricante.
- Executar a validação interna de usabilidade com cinco participantes e registrar resultados por tarefa, sem generalizar a evidência para o público geral. Para a meta de busca de NFR-05, medir ao menos 100 envios aceitos em sete dias, incluindo erros e timeouts, em três ou mais regiões brasileiras.
- Para NFR-06, combinar auditoria automática, revisão manual, testes completos por teclado e leitor de tela; registrar ferramentas/versões, estados testados e violações confirmadas.

## Risks & Trade-offs

- **Cliente direto para Open-Meteo:** evita backend, credenciais e operação de servidor no MVP; em contrapartida, disponibilidade, latência e condições de uso dependem do provedor. Alternativa considerada: proxy/backend próprio, que permite controle de cache e resiliência, mas adiciona infraestrutura sem requisito atual. Validar condições de uso e cobertura antes do lançamento.
- **`fetch` nativo e services específicos:** mantém poucas dependências e deixa claro o contrato de cada endpoint. Alternativa: cliente HTTP genérico, que pode simplificar interceptors/retries, mas adiciona abstração desnecessária para duas chamadas públicas.
- **Hooks locais em vez de store global:** mantém escopo de estado perto de cada fluxo, suficiente para busca, duas seções e preferências. Alternativas: Context/Redux ou biblioteca de estado, que oferecem centralização, mas aumentam configuração e acoplamento sem necessidade demonstrada.
- **Clima atual e previsão em requests independentes:** permite estados, falhas, retries e atualização por seção. Alternativa: uma única consulta combinada, que reduz orquestração e possivelmente simplifica chamadas, mas faria uma falha afetar ambas as seções.
- **Celsius como unidade canônica:** simplifica validação e mantém um único conjunto de dados; Fahrenheit é derivado na renderização, evitando refetch e divergência entre unidades. Alternativa: solicitar unidades distintas ao provedor, que transfere conversão, mas requer chamadas ou reconsultas e dificulta consistência.
- **Persistir somente preferências, sem cache meteorológico entre sessões:** reduz dados locais e evita apresentar clima antigo como atual; exige nova consulta ao restaurar a cidade. Alternativa: persistir respostas para exibição imediata offline, incompatível com o requisito de não manter cache meteorológico local entre sessões.
- **Validação estrita com campos opcionais e estados incompletos:** pode mostrar menos informações quando a resposta é imperfeita, mas evita inventar valores ou afirmar validade indevida. Alternativa: preencher lacunas ou tratar toda resposta como sucesso completo, com risco de apresentação enganosa.
- **Datas locais e validade guiadas pelo fuso IANA da API:** preservam o calendário da cidade consultada, mas exigem cuidado ao interpretar timestamps sem offset e calcular o offset local. Alternativa: usar o fuso do dispositivo, mais simples, mas incorreta para cidades em fusos diferentes.
- **Atualização periódica a cada 30 minutos, sem cache compartilhado:** equilibra atualidade e simplicidade no cliente, mas pode gerar tráfego repetido entre usuários. Alternativa: cache central no servidor, que reduz chamadas duplicadas, mas requer backend e política adicional de expiração.
