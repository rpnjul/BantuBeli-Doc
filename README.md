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
                    FROM (SELECT ROWNUM rns,
                                 store.*,
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
                                                 RADIANS ('".$user->latitude."'))
                                            * COS (
                                                   RADIANS ('".$user->longtitude."')
                                                 - RADIANS (longtitude))
                                          +   SIN (RADIANS (latitude))
                                            * SIN (
                                                 RADIANS ('".$user->latitude."')))),
                                    2)
                                    AS jarak
                            FROM store WHERE groups_id = 1 AND PROVINCE = '".$user->province."') x
                   WHERE x.jarak < 10.0 and x.rns < 100
                ORDER BY x.jarak ASC) y
         WHERE y.product_count >= 1) z
 WHERE z.rn <= 8;
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

## CRONJOB TABS SETTINGS

```bash
  crontab -e
```
