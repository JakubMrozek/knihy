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
* Nepoužívat konstanty typu “YESTERDAY” ap., lepší je použít CURRENT_DATE - 1.
* PostgreSQL umožňuje vložit jako offset kromě hh:mm i ss, časová zóna se v historii měnila i o sekundy.
* Časové zóny jsou v tabulce pg_timezone_names.
* Intervaly. Je možné používat dotazy typu SELECT NOW() - INTERVAL '1 year 2 months 3 days'.
* Možno použít tyto boolean hodnoty: TRUE/FALSE, ON/OFF, YES/NO, 1/0.
* Nové datové typy (třeba ENUM) se vytvoří přes CREATE TYPE. ENUM zachovává pořadí podle vložení, je tedy možné dělat dotazy typu SELECT * WHERE current_mood > 'sad'.
* Geometrické typy (POINT, CIRCLE etc), typ BIT, typy pro adresy (MACADDR ap.), UUID typ.
* Typ XML. Neexistují pro něj žádné operátory, takže je jen pro ukládání a získávání dat.
* Typy JSON se ukládá jako text a vždy při získávání dat se musí parsovat. Naopak typ JSONB se zparsuje a poté se uloží, takže je jeho získávání výrazně rychlejší. * JSONB si nepamatuje pořadí klíčů u objektů. 
* Pro fulltext se používá typ TSVECTOR. Text se rozloží na jednotlivé tokeny a pak je možné vyhledávat i jiné tvary slova, definovat jak daleko od sebe slova v textu mohou být vzdáleny atd. Bohužel chybí podpora jazyků typu čínština, japonština ap.
* Dotazy zamykají celý řádek. Takže když je potřeba provádět řada operací jako prohledávání JSON struktury, concurrency může být problém.
* Používat na JSONB indexy. Liší se podle toho, jaké operace umožňují nad daty provádět.
* Získávání dat přes dotazy typu SQL/JSON. Např. $.floor[0, 1].apt[1 to last].
* Pro fulltext se používá typ TSVECTOR. Text se rozloží na jednotlivé tokeny a pak je možné vyhledávat i jiné tvary slova, definovat jak daleko od sebe slova v textu mohou být vzdáleny atd. Bohužel chybí podpora jazyků typu čínština, japonština ap.
* Typ ARRAY. Definuje se jako typ[]. Je možné zadat typ i jako integer[3], ale ta trojka slouží jen pro dokumentaci, PostgreSQL počet prvků v poli nehlídá.  Možno používat vícedimenziální pole. Vybírání rozsahu pole přes dotazy typu schedule[1:2][1:1].



## Zdroje
* https://wiki.postgresql.org/wiki/Don%27t_Do_This#Don.27t_use_money 
