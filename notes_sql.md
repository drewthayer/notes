## Create Column
`# ALTER TABLE tablename ADD COLUMN colname DOUBLE PRECISION;` # or other datatype

## FILL MULTIPLE NEW COLUMNS FROM QUERY
~~~
UPDATE rachio_savings r
SET savings_w_def = sub.weighted_savings, risk_score_def = sub.score
FROM (
	SELECT s.device_id, s.year, s.savings * r.w_awr_def_qan_score / 5.0 as weighted_savings, r.w_awr_def_qan_score as score
	FROM rachio_savings s
	JOIN risk_polygons r ON ST_CONTAINS(r.shape, s.geom)
) sub
WHERE r.device_id = sub.device_id AND r.year = sub.year;
~~~
