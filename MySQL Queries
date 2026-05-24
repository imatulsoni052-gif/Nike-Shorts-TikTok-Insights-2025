#-Platform Comparison: Total views and average engagement_rate across YouTube Shorts vs TikTok.
SELECT 
    platform,
    SUM(total_views) AS total_views,
   round( AVG(avg_engagement_per_1k),2) AS engagement_rate
FROM
    country_platform_summary_2025
GROUP BY platform;
#-Regional Hotspots: Top 5 countries with the highest views for Nike campaigns on each platform.
WITH ranked_views AS (
  SELECT 
    platform,
    country,
    SUM(total_views) AS total_views,
ROW_NUMBER() OVER (PARTITION BY platform ORDER BY SUM(total_views) DESC)
    AS rn
  FROM country_platform_summary_2025 
  GROUP BY platform, country)
SELECT 
  platform,country,total_views
FROM ranked_views
WHERE rn <= 5
ORDER BY platform, rn;

#-Category Performance: Average completion_rate and avg_watch_time_sec for categories like sports, lifestyle, fashion.
SELECT 
    category,
    ROUND(AVG(completion_rate), 2) AS completion_rate,
    ROUND(AVG(avg_watch_time_sec), 2) AS avg_watch_time_sec
FROM
    youtube_shorts_tiktok_trends_2025
WHERE
    category IN ('sports' , 'lifestyle', 'fashion')
GROUP BY category;

#--Creator Impact: Top 10 author_handle by average views and their creator_tier.
SELECT 
    author_handle,
    creator_tier,
    ROUND(AVG(creator_avg_views), 2) AS avg_views
FROM
    youtube_shorts_tiktok_trends_2025
GROUP BY author_handle , creator_tier
ORDER BY avg_views DESC
LIMIT 10;

#-Hashtag ROI: Top 20 hashtags by total views and their median engagement_rate.

WITH hashtag_data AS (
    SELECT hashtag, views, total_engagements,
        total_engagements * 1.0 / NULLIF(views, 0) AS engagement_rate
    FROM top_hastags_2025
    WHERE views > 0),
top_20 AS ( SELECT hashtag, SUM(views) AS total_view FROM hashtag_data
    GROUP BY hashtag
    ORDER BY total_views DESC LIMIT 20 ),
ranked AS ( SELECT hashtag, engagement_rate,
        ROW_NUMBER() OVER (PARTITION BY hashtag ORDER BY engagement_rate)
        AS rn,
        COUNT(*) OVER (PARTITION BY hashtag) AS cnt
    FROM hashtag_data )
SELECT t.hashtag, t.total_views,
   round( AVG(r.engagement_rate),2) AS median_engagement_rate
FROM top_20 t
JOIN ranked r
    ON t.hashtag = r.hashtag
WHERE r.rn IN ((r.cnt + 1) / 2, (r.cnt + 2) / 2)
GROUP BY t.hashtag, t.total_views
ORDER BY t.total_views DESC;

#--Emoji Effect: Compare median engagement_per_1k between videos with and without has_emoji in titles.
WITH Categorized AS ( SELECT engagement_per_1k,
        CASE 
            WHEN LENGTH(title) > CHAR_LENGTH(title) THEN 'Has Emoji' 
            ELSE 'No Emoji' 
        END AS has_emoji,
ROW_NUMBER() OVER(
	PARTITION BY (CASE WHEN LENGTH(title) > CHAR_LENGTH(title) THEN 'Has Emoji' ELSE 'No Emoji' END) 
            ORDER BY engagement_per_1k
        ) AS row_idx,
        COUNT(*) OVER(
     PARTITION BY (CASE WHEN LENGTH(title) > CHAR_LENGTH(title) THEN 'Has Emoji' ELSE 'No Emoji' END)
        ) AS total_count
    FROM youtube_shorts_tiktok_trends_2025 )
SELECT 
    has_emoji,
    round(AVG(engagement_per_1k),2) AS median_engagement
FROM Categorized
WHERE row_idx IN (FLOOR((total_count + 1) / 2), CEIL((total_count + 1) / 2))
GROUP BY has_emoji;

#--Upload Timing: Average views and completion_rate by publish_dayofweek and upload_hour.
SELECT 
    publish_dayofweek,
    upload_hour,
    ROUND(AVG(views), 2) AS average_view,
    ROUND(AVG(completion_rate), 2) AS average_completion_rate
FROM
    youtube_shorts_tiktok_trends_2025
GROUP BY publish_dayofweek , upload_hour;

#--Trend Momentum: Median engagement_velocity and trend_duration_days by trend_type.
WITH a AS (
    SELECT 
        trend_type,
        engagement_velocity,
        trend_duration_days,
        ROW_NUMBER() OVER(PARTITION BY trend_type ORDER BY engagement_velocity) 
        AS row_idx_vel,
        ROW_NUMBER() OVER(PARTITION BY trend_type ORDER BY trend_duration_days) 
        AS row_idx_dur,
        COUNT(*) OVER(PARTITION BY trend_type) AS total_count
    FROM youtube_shorts_tiktok_trends_2025
)
SELECT 
    trend_type,
   round( AVG(engagement_velocity),2) AS median_velocity,
   round( AVG(trend_duration_days),2) AS median_duration
FROM a
WHERE row_idx_vel IN (FLOOR((total_count + 1) / 2), CEIL((total_count + 1) / 2))
   OR row_idx_dur IN (FLOOR((total_count + 1) / 2), CEIL((total_count + 1) / 2))
GROUP BY trend_type;

#--Device Analysis: Completion rates by device_type (e.g., mobile vs tablet) and device_brand.
SELECT 
    device_type,
    device_brand,
    ROUND(AVG(completion_rate), 2) AS completion_rate
FROM
    youtube_shorts_tiktok_trends_2025
GROUP BY device_type , device_brand;

#--Traffic Sources: Breakdown of traffic_source (e.g., For You Page, search, direct) with median completion_rate
with a as(select traffic_source, completion_rate, 
row_number() over(partition by traffic_source order by completion_rate)
as row_idx_ts
,count(*) over(partition by traffic_source) as total_count
from youtube_shorts_tiktok_trends_2025)
select traffic_source,
round(avg(completion_rate),2)as median_completion_rate
from a where row_idx_ts in
 (floor((total_count +1)/2),ceil((total_count + 1)/2))
group by traffic_source ;

#--Seasonal Insights: Compare views and engagement_rate across event_season (e.g., Olympics, Christmas, Summer sales)
 SELECT 
    event_season,
    ROUND(AVG(views), 2) AS average_views,
    ROUND(AVG(engagement_rate), 2) AS average_engagement_rate
FROM
    youtube_shorts_tiktok_trends_2025
GROUP BY event_season;
 
#--Data Quality Check: Validate if engagement_total = likes + comments + shares + saves. Return mismatched rows.
SELECT 
    engagement_total,
    CASE
        WHEN engagement_total = likes + comments + shares + saves THEN 'matched_rows'
        ELSE 'mismatched_rows'
    END AS quality_check
FROM
    youtube_shorts_tiktok_trends_2025;










