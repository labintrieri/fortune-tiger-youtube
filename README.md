# Fortune Tiger no YouTube: Investigação sobre propaganda enganosa

Repositório de dados da investigação jornalística sobre vídeos de propaganda do Fortune Tiger (conhecido como "jogo do tigrinho") no YouTube.

## Publicação

**[YouTube abriga 3.500 vídeos de propaganda enganosa de tigrinho e caça-níqueis online](https://www1.folha.uol.com.br/mercado/2024/08/youtube-abriga-3500-videos-de-propaganda-enganosa-de-tigrinho-e-caca-niqueis-online.shtml)** - Folha de S.Paulo, agosto de 2024

Reportagem também publicada no Terra Byte:
- [Fortune Tiger: jogo ilegal na mira da polícia inundou YouTube neste ano](https://www.terra.com.br/byte/fortune-tiger-jogo-ilegal-na-mira-da-policia-inundou-youtube-neste-ano,28a1beb68d4f8b68d28e7aefd6e231e78bq61r5r.html)

## Contexto

O Fortune Tiger é um jogo de caça-níquel online operado por plataformas de apostas ilegais no Brasil. Em 2023, uma onda de vídeos no YouTube passou a promover o jogo com promessas de ganhos fáceis, utilizando termos como "estratégia", "robô", "bug" e "ganhar dinheiro".

Esta investigação identificou e catalogou vídeos que promovem o Fortune Tiger, analisando padrões de publicação, canais envolvidos e características do conteúdo.

## Metodologia

### 1. Extração automatizada

Coleta de metadados de vídeos do YouTube via API, utilizando a busca pelo termo "Fortune Tiger" em três períodos de 2023:

| Período | Arquivo | Vídeos |
|---------|---------|--------|
| 01/01 a 16/03 | `_fortune tiger_ - 01_01 a 16_03 - videolist_search222_*.csv` | 221 |
| 16/03 a 21/05 | `_fortune tiger_ - 16_03 a 21_05 - videolist_search476_*.csv` | 475 |
| 21/05 a 28/09 | `_fortune tiger_ - 21_05 a 28_09 - videolist_search474_*.csv` | 473 |

**Total de vídeos coletados:** 1.169

### 2. Consolidação e análise

Os dados foram unificados no arquivo `ft_union.csv` e analisados via SQL. O arquivo `analise.md` contém as queries utilizadas.

### 3. Classificação por palavras-chave

Para identificar vídeos com características de propaganda enganosa, foi aplicado um filtro por palavras-chave em títulos, descrições e tags:

- **Promessas de ganhos:** "pagando", "faturar", "ganhar"
- **Falsas estratégias:** "estratégia", "tática", "dicas", "robô"
- **Clickbait:** "funciona", "tentei", "testei"
- **Termos de apostas:** "banca", "slot"

## Estrutura dos dados

### Campos do CSV

| Campo | Descrição |
|-------|-----------|
| `videoId` | ID único do vídeo no YouTube |
| `channelId` | ID do canal |
| `channelTitle` | Nome do canal |
| `videoTitle` | Título do vídeo |
| `videoDescription` | Descrição do vídeo |
| `tags` | Tags associadas ao vídeo |
| `publishedAt` | Data de publicação (ISO 8601) |
| `publishedAtSQL` | Data de publicação (formato SQL) |
| `viewCount` | Número de visualizações |
| `likeCount` | Número de likes |
| `commentCount` | Número de comentários |
| `duration` | Duração do vídeo |
| `videoCategoryLabel` | Categoria do vídeo |
| `licensedContent` | Se o conteúdo é licenciado |
| `thumbnail_maxres` | URL da thumbnail em alta resolução |

## Arquivos do repositório

```
.
├── README.md                           # Este arquivo
├── LICENSE                             # Licença MIT
├── analise.md                          # Queries SQL utilizadas na análise
├── ft_union.csv                        # Base consolidada (1.169 vídeos)
└── _fortune tiger_ - *.csv             # Arquivos de extração por período
```

## Como usar os dados

### Carregar no Python (pandas)

```python
import pandas as pd

df = pd.read_csv('ft_union.csv')
print(f"Total de vídeos: {len(df)}")
print(df.head())
```

### Carregar no SQLite

```sql
-- Importar CSV
.mode csv
.import ft_union.csv ft_union

-- Consultar vídeos por canal
SELECT channelTitle, COUNT(*) as total
FROM ft_union
GROUP BY channelTitle
ORDER BY total DESC
LIMIT 10;
```

## Licença

Este repositório está licenciado sob a [MIT License](LICENSE).

## Contato

Para questões sobre a metodologia ou os dados, entre em contato através das issues deste repositório.
