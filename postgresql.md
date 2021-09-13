# PostgreSQL
## 8. Datové typy
* Nové datové typy (třeba ENUM) se vytvoří přes CREATE TYPE.
* Pro přesná čísla používat jen NUMERIC(počet cifer, počet des. čísel), jeho nevýhoda je, že kalkulace trvá o dost déle než u nepřesných REAL a DOUBLE PRECISION.
* Typ SERIAL je zastaralý, lepší je definovat primární klíč pomocí standardizovaného INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY.
* Typ MONEY umožňuje zadávat i hodnoty jako $1,000.00, nepoužívat.
* Pro textové hodnoty používat jen VARCHAR(X) a neomezený TEXT. Typ CHAR(X) doplní řetězec o prázdné hodnoty na požadovanou délku.

## Zdroje
* https://wiki.postgresql.org/wiki/Don%27t_Do_This#Don.27t_use_money 
