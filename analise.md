# Análises SQL

```sql
-- Juntar tabelas
CREATE TABLE ft_union AS SELECT * FROM ft_1 WHERE 0;
INSERT INTO ft_union SELECT * FROM ft_1;
INSERT INTO ft_union SELECT * FROM ft_2;
INSERT INTO ft_union SELECT * FROM ft_3;
COMMIT;

-- Ordenar por datas
SELECT * FROM ft_union ORDER BY publishedAtSQL DESC;

-- Contar o número de vídeos por canal
SELECT channelTitle, COUNT(*) AS video_count
FROM ft_union
GROUP BY channelTitle
ORDER BY video_count DESC;

-- Contagem de Vídeos Publicados por Mês
SELECT 
    strftime('%Y-%m', publishedAtSQL) AS month,
    COUNT(*) AS video_count
FROM 
    ft_union
GROUP BY 
    month
ORDER BY 
    month;

-- Contagem de Vídeos Licenciados versus Não Licenciados
SELECT 
    licensedContent,
    COUNT(*) AS video_count
FROM 
    ft_union
GROUP BY 
    licensedContent;

-- Média de Visualizações, Likes e Dislikes para Vídeos Licenciados versus Não Licenciados
SELECT 
    licensedContent,
    AVG(viewCount) AS average_views,
    AVG(likeCount) AS average_likes,
    AVG(dislikeCount) AS average_dislikes
FROM 
    ft_union
GROUP BY 
    licensedContent;

-- Canais com Maior Número de Vídeos Licenciados
SELECT 
    channelTitle,
    COUNT(*) AS licensed_video_count
FROM 
    ft_union
WHERE 
    licensedContent = 1
GROUP BY 
    channelTitle
ORDER BY 
    licensed_video_count DESC;

-- Agrupar por categorias
SELECT
    videoCategoryLabel,
    AVG(viewCount) AS average_views,
    AVG(likeCount) AS average_likes,
    AVG(dislikeCount) AS average_dislikes
FROM
    ft_union
GROUP BY
    videoCategoryLabel;

-- Agrupar por títulos
SELECT 
    videoTitle,
    COUNT(*) AS video_count
FROM 
    ft_union
GROUP BY 
    videoTitle
HAVING 
    video_count > 0
ORDER BY 
    video_count DESC;

-- Selecionar títulos populares
CREATE TABLE ft_titulos_pop AS
SELECT * FROM ft_union 
WHERE videoTitle = '😻Fortune tiger NOVA estrategia GANHAR MUITO com BANCA BAIXA'
   OR videoTitle = 'Fortune Tiger Estratégia? Fortune Tiger o Melhor Robo - Bug Fortune Tiger Subir Banca Jogo do Tigre'
ORDER BY publishedAtSQL DESC;

-- Tabela com palavras-chave
CREATE TABLE ft_union_keywords AS
SELECT * FROM ft_union
WHERE
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
    LOWER(tags) LIKE '%pagando%' OR
    LOWER(tags) LIKE '%faturar%' OR
    LOWER(tags) LIKE '%estratégia%' OR
    LOWER(tags) LIKE '%ganhar%' OR
    LOWER(tags) LIKE '%robô%' OR
    LOWER(tags) LIKE '%tática%' OR
    LOWER(tags) LIKE '%dicas%' OR
    LOWER(tags) LIKE '%funciona%' OR
    LOWER(tags) LIKE '%tentei%' OR
    LOWER(tags) LIKE '%testei%'OR
    LOWER(tags) LIKE '%banca%' OR
    LOWER(tags) LIKE '%slot%';

-- Tabela com títulos que não contêm as palavras-chave
CREATE TABLE ft_union_remaining AS
SELECT ft_union.*
FROM ft_union
LEFT JOIN ft_union_keywords
ON ft_union.videoId = ft_union_keywords.videoId
WHERE ft_union_keywords.videoId IS NULL;

-- Encontrar a data do primeiro e do último vídeo publicado por cada canal
CREATE TABLE ft_union_dates AS
SELECT 
    channelTitle, 
    MIN(publishedAtSQL) AS first_video_date, 
    MAX(publishedAtSQL) AS last_video_date
FROM 
    ft_union_keywords
GROUP BY 
    channelTitle
ORDER BY first_video_date DESC;

-- Contar o número de vídeos por canal
SELECT channelTitle, COUNT(*) AS video_count
FROM ft_union_keywords
GROUP BY channelTitle
ORDER BY video_count DESC;

-- ESPIGA 0.3
SELECT* FROM ft_union_keywords
WHERE channelTitle is ('ESPIGA 0.3');
```
