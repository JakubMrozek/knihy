# PostgreSQL
* https://www.postgresql.org/docs/current/index.html

## 4. SQL syntax
* Identifkátory dvojíté uvozovky, stringy jednoduché. 
* Uvozovky se zdvojují, pokud jsou obsahem řetězce ( 'Dianne''s horse'). Dále je možné použít zápis pomocí znaků dolaru: $$Dianne's horse$$.
* U identifikátorů se neřeší velikost písmen, pokud nejsou v uvozovkách. V uvozovkách je možné použít takřka jakýkoliv název identifikátoru (včetně mězer). Např. UPDATE table SET X = 1 WHERE x = 10 AND “my sloupec” = 5.
* Přetypování provést nejlépe přes standardní funkci CAST(). Ostatní způsoby (::) jsou buď jen pro PostgreSQL nebo mají jiné problémy.
* COLLATE se používá pro definici způsobu řazení, např. můžeme říct, že chceme řadit podle německých jazykových pravidel ap.
* Jako náhrada IFů je možné použít zápis s CASE, ale měli bychom se mu vyhýbat, problém je s optimalizací.
* U funkcí je možné parametr pojmenovat, např. SELECT concat_lower_or_upper(a => 'Hello', b => 'World').
* Funkce ROW() se hodí, když chceme zacházet s řádkem jako celkem, např. SELECT ROW('a', 'b') = (SELECT 'a', 'b') .
* Window funkce. Umožňují pracovat se setem hodnot, které se vzdahují k danému řádku.

## 5. Data definition
* Generated columns fungují podobně jako views, sloupec je vytvořen z hodnot jiných sloupců. Nelze editovat přímo. Má dva druhy: stored (hodnota sloupce je fyzicky ukládaná) a virtual (hodnota je vypočítaná při výběru). Např. height_in numeric GENERATED ALWAYS AS (height_cm / 2.54) STORED. 
* K typům sloupců je možné přidat specifičtější kontrolu pomocí CHECK. Např. price numeric CHECK (price > 0).
* K sloupcům, které odkazují na jiné sloupce (cizí klíče), je dobré přidat index. Pro SELECTy to obvykle nevadí, protože v odkazované tabulce už index na primárním klíči je, ale pokud provádíme operace typu WHERE order_id = XXX, tak by měl být index na order_id.

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
* Kompozitní (složené) typy. Jsou užitečné při práci s procedurami, jinak raději nepoužívat. Vložení jako INSERT INTO on_hand VALUES (ROW('fuzzy dice', 42, 1.99).
* RANGE typ pro rozsah třeba čísel nebo dat. Ideální třeba pro uložení informace, že mistnost bude nedostupná OD - DO.
* DOMAIN se používá pro kontrolu hodnot. Třeba zda je řetězec typu email, číslo je kladné ap.

## Zdroje
* https://wiki.postgresql.org/wiki/Don%27t_Do_This#Don.27t_use_money 
