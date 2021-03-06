# Setup MariaDB

## Connect

### Port forward

```bash
kubectl port-forward svc/mariadb-service 3306:3306
```

### Connect

```bash
mysql -h 127.0.0.1 -P 3306 -u master -pmypw develop
```

---

## Setup

### Create a table

```sql
CREATE TABLE pet (name VARCHAR(20), owner VARCHAR(20), species VARCHAR(20), sex CHAR(1), birth DATE, death DATE);
```

```sql
DESCRIBE pet;

+---------+-------------+------+-----+---------+-------+
| Field   | Type        | Null | Key | Default | Extra |
+---------+-------------+------+-----+---------+-------+
| name    | varchar(20) | YES  |     | NULL    |       |
| owner   | varchar(20) | YES  |     | NULL    |       |
| species | varchar(20) | YES  |     | NULL    |       |
| sex     | char(1)     | YES  |     | NULL    |       |
| birth   | date        | YES  |     | NULL    |       |
| death   | date        | YES  |     | NULL    |       |
+---------+-------------+------+-----+---------+-------+
```

### Insert data

```sql
INSERT INTO pet
VALUES ('Fluffy', 'Harold', 'cat', 'f', '1993-02-04', NULL),
('Claws', 'Gwen', 'cat', 'm', '1994-03-17', NULL),
('Buffy', 'Harold', 'dog', 'f', '1989-05-13', NULL),
('Fang', 'Benny', 'dog', 'm', '1990-08-27', NULL),
('Bowser', 'Diane', 'dog', 'm', '1979-08-31', '1995-07-29'),
('Chirpy', 'Gwen', 'bird', 'f', '1998-09-11', NULL),
('Whistler', 'Gwen', 'bird', NULL, '1997-12-09', NULL),
('Slim', 'Benny', 'snake', 'm', '1996-04-29', NULL),
('Puffball', 'Diane', 'hamster', 'f', '1999-03-30', NULL);
```

### Selecting all data

```sql
SELECT * FROM pet;

+----------+--------+---------+------+------------+------------+
| name     | owner  | species | sex  | birth      | death      |
+----------+--------+---------+------+------------+------------+
| Fluffy   | Harold | cat     | f    | 1993-02-04 | NULL       |
| Claws    | Gwen   | cat     | m    | 1994-03-17 | NULL       |
| Buffy    | Harold | dog     | f    | 1989-05-13 | NULL       |
| Fang     | Benny  | dog     | m    | 1990-08-27 | NULL       |
| Bowser   | Diane  | dog     | m    | 1979-08-31 | 1995-07-29 |
| Chirpy   | Gwen   | bird    | f    | 1998-09-11 | NULL       |
| Whistler | Gwen   | bird    | NULL | 1997-12-09 | NULL       |
| Slim     | Benny  | snake   | m    | 1996-04-29 | NULL       |
| Puffball | Diane  | hamster | f    | 1999-03-30 | NULL       |
+----------+--------+---------+------+------------+------------+
```

```bash
exit
```

---

## Backup

### Get a pod

```bash
kubectl get pod --selector=app=mariadb --template '{{range .items}}{{.metadata.name}}{{end}}'
```

### Create database dumps

```bash
kubectl exec $(kubectl get pod --selector=app=mariadb --template '{{range .items}}{{.metadata.name}}{{end}}') -c db \
-- sh -c 'exec mysqldump --all-databases -umaster -pmypw' > dump.sql
```

### Restore data from dump files

```bash
kubectl exec -i $(kubectl get pod --selector=app=mariadb --template '{{range .items}}{{.metadata.name}}{{end}}') -c db \
-- sh -c 'exec mysql -umaster -pmypw' < db/mariadb/data/dump.sql
```
