# Bantu Beli Documentation
Bantu Beli Documentation for Supporting Developing

---
## Navigation
[-] [RADIANS FUNCTION DATABASE](#radians-function-database)

[-] [QUERY PERHITUNGAN JARAK LONGLAT](#query-perhitungan-jarak-longlat)

[-] [QUERY SELECT ALL WITH GROUP BY ORACLE](#query-select-all-with-group-by-oracle)

[-] [QUERY SELECT DATA MASTER ALAMAT BY KODE POS](#query-select-data-master-alamat-by-kode-pos)

[-] [Laravel Query Log](#laravel-query-log)

[-] [CRONJOB TABS SETTINGS](#cronjob-tabs-settings)


---

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

## QUERY SELECT DATA MASTER ALAMAT BY KODE POS
```sql
SELECT DATA_POST.ID,
       DATA_POST.KODE_POS,
       DATA_DESA.NAMA_DESA,
       DATA_KECAMATAN.NAMA_KECAMATAN,
       DATA_KOTA.NAMA_KOTA,
       DATA_PROVINSI.NAMA_PROVINSI
  FROM DATA_POST
       JOIN DATA_PROVINSI
          ON DATA_PROVINSI.ID = DATA_POST.PROVINSI_ID
       JOIN DATA_KOTA
          ON DATA_KOTA.ID = DATA_POST.KOTA_ID
       JOIN DATA_KECAMATAN
          ON DATA_KECAMATAN.ID = DATA_POST.KECAMATAN_ID
       JOIN DATA_DESA
          ON DATA_DESA.ID = DATA_POST.DESA_ID
 WHERE KODE_POS = '13530'
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
