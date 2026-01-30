# Análise dos Dados - Fortune Tiger no YouTube

Este documento contém as queries SQL utilizadas para analisar os vídeos de propaganda do Fortune Tiger coletados do YouTube.

## Preparação dos dados

### Consolidação das tabelas

Unificação dos três arquivos CSV de extração em uma tabela única:

```sql
-- Criar tabela unificada a partir da estrutura de ft_1
CREATE TABLE ft_union AS SELECT * FROM ft_1 WHERE 0;

-- Inserir dados de todas as extrações
INSERT INTO ft_union SELECT * FROM ft_1;
INSERT INTO ft_union SELECT * FROM ft_2;
INSERT INTO ft_union SELECT * FROM ft_3;
COMMIT;
```

## Análises exploratórias

### Ordenação cronológica

```sql
-- Visualizar todos os vídeos ordenados por data de publicação
SELECT * FROM ft_union ORDER BY publishedAtSQL DESC;
```

### Contagem de vídeos por canal

Identificação dos canais mais ativos na publicação de conteúdo sobre Fortune Tiger:

```sql
SELECT channelTitle, COUNT(*) AS video_count
FROM ft_union
GROUP BY channelTitle
ORDER BY video_count DESC;
```

### Evolução temporal

Distribuição de vídeos publicados por mês:

```sql
SELECT
    strftime('%Y-%m', publishedAtSQL) AS month,
    COUNT(*) AS video_count
FROM ft_union
GROUP BY month
ORDER BY month;
```

## Análise de conteúdo licenciado

### Proporção de vídeos licenciados

```sql
SELECT
    licensedContent,
    COUNT(*) AS video_count
FROM ft_union
GROUP BY licensedContent;
```

### Métricas de engajamento por tipo de licenciamento

```sql
SELECT
    licensedContent,
    AVG(viewCount) AS average_views,
    AVG(likeCount) AS average_likes,
    AVG(dislikeCount) AS average_dislikes
FROM ft_union
GROUP BY licensedContent;
```

### Canais com mais conteúdo licenciado

```sql
SELECT
    channelTitle,
    COUNT(*) AS licensed_video_count
FROM ft_union
WHERE licensedContent = 1
GROUP BY channelTitle
ORDER BY licensed_video_count DESC;
```

## Análise por categoria

Métricas de engajamento agrupadas por categoria de vídeo:

```sql
SELECT
    videoCategoryLabel,
    AVG(viewCount) AS average_views,
    AVG(likeCount) AS average_likes,
    AVG(dislikeCount) AS average_dislikes
FROM ft_union
GROUP BY videoCategoryLabel;
```

## Identificação de títulos duplicados

Detecção de vídeos com títulos idênticos (possível indicador de spam ou coordenação):

```sql
SELECT
    videoTitle,
    COUNT(*) AS video_count
FROM ft_union
GROUP BY videoTitle
HAVING video_count > 1
ORDER BY video_count DESC;
```

## Filtragem por palavras-chave

### Identificação de propaganda enganosa

Criação de tabela com vídeos que contêm palavras-chave associadas a promessas de ganhos e táticas enganosas:

```sql
CREATE TABLE ft_union_keywords AS
SELECT * FROM ft_union
WHERE
    -- Palavras-chave em títulos
    LOWER(videoTitle) LIKE '%pagando%' OR
    LOWER(videoTitle) LIKE '%faturar%' OR
    LOWER(videoTitle) LIKE '%estratégia%' OR
    LOWER(videoTitle) LIKE '%ganhar%' OR
    LOWER(videoTitle) LIKE '%robô%' OR
    LOWER(videoTitle) LIKE '%tática%' OR
    LOWER(videoTitle) LIKE '%dicas%' OR
    LOWER(videoTitle) LIKE '%funciona%' OR
    LOWER(videoTitle) LIKE '%tentei%' OR
    LOWER(videoTitle) LIKE '%testei%' OR
    LOWER(videoTitle) LIKE '%banca%' OR
    LOWER(videoTitle) LIKE '%slot%' OR
    -- Palavras-chave em descrições
    LOWER(videoDescription) LIKE '%pagando%' OR
    LOWER(videoDescription) LIKE '%faturar%' OR
    LOWER(videoDescription) LIKE '%estratégia%' OR
    LOWER(videoDescription) LIKE '%ganhar%' OR
    LOWER(videoDescription) LIKE '%robô%' OR
    LOWER(videoDescription) LIKE '%tática%' OR
    LOWER(videoDescription) LIKE '%dicas%' OR
    LOWER(videoDescription) LIKE '%funciona%' OR
    LOWER(videoDescription) LIKE '%tentei%' OR
    LOWER(videoDescription) LIKE '%testei%' OR
    LOWER(videoDescription) LIKE '%banca%' OR
    LOWER(videoDescription) LIKE '%slot%' OR
    -- Palavras-chave em tags
    LOWER(tags) LIKE '%pagando%' OR
    LOWER(tags) LIKE '%faturar%' OR
    LOWER(tags) LIKE '%estratégia%' OR
    LOWER(tags) LIKE '%ganhar%' OR
    LOWER(tags) LIKE '%robô%' OR
    LOWER(tags) LIKE '%tática%' OR
    LOWER(tags) LIKE '%dicas%' OR
    LOWER(tags) LIKE '%funciona%' OR
    LOWER(tags) LIKE '%tentei%' OR
    LOWER(tags) LIKE '%testei%' OR
    LOWER(tags) LIKE '%banca%' OR
    LOWER(tags) LIKE '%slot%';
```

### Vídeos sem palavras-chave

Tabela com vídeos que não contêm as palavras-chave (para análise complementar):

```sql
CREATE TABLE ft_union_remaining AS
SELECT ft_union.*
FROM ft_union
LEFT JOIN ft_union_keywords
ON ft_union.videoId = ft_union_keywords.videoId
WHERE ft_union_keywords.videoId IS NULL;
```

## Análise temporal por canal

Identificação do período de atividade de cada canal:

```sql
CREATE TABLE ft_union_dates AS
SELECT
    channelTitle,
    MIN(publishedAtSQL) AS first_video_date,
    MAX(publishedAtSQL) AS last_video_date
FROM ft_union_keywords
GROUP BY channelTitle
ORDER BY first_video_date DESC;
```

## Análises específicas

### Títulos populares replicados

Investigação de títulos específicos que aparecem múltiplas vezes:

```sql
CREATE TABLE ft_titulos_pop AS
SELECT * FROM ft_union
WHERE videoTitle = '😻Fortune tiger NOVA estrategia GANHAR MUITO com BANCA BAIXA'
   OR videoTitle = 'Fortune Tiger Estratégia? Fortune Tiger o Melhor Robo - Bug Fortune Tiger Subir Banca Jogo do Tigre'
ORDER BY publishedAtSQL DESC;
```

### Ranking de canais com palavras-chave

```sql
SELECT channelTitle, COUNT(*) AS video_count
FROM ft_union_keywords
GROUP BY channelTitle
ORDER BY video_count DESC;
```

### Consulta de canal específico

```sql
SELECT * FROM ft_union_keywords
WHERE channelTitle = 'ESPIGA 0.3';
```
