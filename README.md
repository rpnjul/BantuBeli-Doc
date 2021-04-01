# Bantu Beli Documentation
Bantu Beli Documentation for Supporting Developing

## RADIANS FUNCTION DATABASE
RADIANS UNTUK MENGHITUNG JARAK LONGITUDE DAN LATITUDE

```sql
CREATE OR REPLACE FUNCTION MARKETPLACE.RADIANS(nDEGREES IN NUMBER)
  RETURN NUMBER DETERMINISTIC IS
  BEGIN
  RETURN nDegrees / (180/ACOS(-1));
 END RADIANS;
/
```

## QUERY PERHITUNGAN JARAK LONGLAT

```sql
SELECT z.*
  FROM (SELECT ROWNUM rn, y.*
          FROM (  SELECT x.*,
                         (SELECT COUNT (1)
                            FROM bb_products
                           WHERE store_id = x.id)
                            AS product_count
                    FROM (SELECT store.*,
                                 (SELECT CONCAT ('https://www.bantubeli.com',
                                                 img)
                                    FROM store_groups
                                   WHERE id = store.groups_id)
                                    AS logo,
                                 ROUND (
                                    (  6371
                                     * ACOS (
                                              COS (RADIANS (latitude))
                                            * COS (
                                                 RADIANS (-6.188284862877379))
                                            * COS (
                                                   RADIANS (106.82262846921707)
                                                 - RADIANS (longtitude))
                                          +   SIN (RADIANS (latitude))
                                            * SIN (
                                                 RADIANS (-6.188284862877379)))),
                                    2)
                                    AS jarak
                            FROM store
                           WHERE groups_id = 1 AND ROWNUM <= 12) x
                   WHERE x.jarak < 10.0
                ORDER BY x.jarak ASC) y
         WHERE y.product_count >= 1) z
 WHERE z.rn <= 4;
```

## QUERY SELECT ALL WITH GROUP BY ORACLE

```sql
select * from bb_products WHERE rowid in (SELECT MIN(rowid) FROM bb_products GROUP BY sku) ORDER BY ID ASC;
```
## Laravel Query Log

```php
DB::enableQueryLog();
DB::getQueryLog();
```
