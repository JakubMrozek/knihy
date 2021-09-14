# PostgreSQL
* https://www.postgresql.org/docs/current/index.html

## 8. Datové typy
* Nové datové typy (třeba ENUM) se vytvoří přes CREATE TYPE.
* Pro přesná čísla používat jen NUMERIC(počet cifer, počet des. čísel), jeho nevýhoda je, že kalkulace trvá o dost déle než u nepřesných REAL a DOUBLE PRECISION.
* Typ SERIAL je zastaralý, lepší je definovat primární klíč pomocí standardizovaného INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY.
* Typ MONEY umožňuje zadávat i hodnoty jako $1,000.00, nepoužívat.
* Pro textové hodnoty používat jen VARCHAR(X) a neomezený TEXT. Typ CHAR(X) doplní řetězec o prázdné hodnoty na požadovanou délku.
* Nepoužívat typ TIMESTAMP WITHOUT TIME ZONE, vždy používat TIMESTAMP WITH TIME ZONE (TIMESTAMPTZ). V prvním případě se uloží čas jak byl vložen (bez informace o časkové zóně), v druhém případě se ukládá datum jako UTC, takže se dá snadno konvertovat.
* Nepoužívat TIMETZ, místo toho použít TIMESTAMPTZ. 
* Nepoužívvat CURRENT_TIME, protože vrací TIMETZ. Použít místo toho CURRENT_TIMESTAMP.
* Typ INTERVAL umožňuje vložit hodnoty jako YEAR, MONTH ap.


## Zdroje
* https://wiki.postgresql.org/wiki/Don%27t_Do_This#Don.27t_use_money 
