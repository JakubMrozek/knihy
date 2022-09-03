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
* Systém práv typu ALTER TABLE table_name OWNER TO new_owner a GRANT UPDATE ON accounts TO joe.
* Row-Level Security. Umožňuje nařídit přístup jen k některým řádkům (a sloupcům). Uživatel třeba může editovat své heslo, ale už nikoliv hesla jiných uživatelů. Pozor na export dat, mohly by se vyexportovat tabulky bez některých dat (row_security=off).
* Nejvýše je databázový cluster, který obsahuje několik schémat, které pak obsahují různé tabulky.
* Defaultní je public schéma. Rezerované schéma je pg_.
* K čemu se hodí schéma? Nedokází ke kolizím. Můžeme tabulky rozdělit do tématických celků, dobré je to tehdy, když instalujeme třeba rozšíření od třetí strany, tehdy by ke konfliktu názvů mohlo dojít.
* Nastavení cesty, ve které se vyhledává schéma podle příkazu SET search_path TO myschema,public.
* Dědičnost tabulek. Při dotazu SELECT … se použije klauzule ONLY, pokud chceme vybrat data jen ze jmenované tabulky, jinak se vyberou data i pod potomků.
* Zjistit tabulku (její ID) lze přes tableoid.
* Smazat i všechny závislé objekty přes DROP xxx CASCADE. 
* Partitioning rozdělí tabulku na několik menších celků. Benefit je v tom, že pak prohledávám menší množství dat, což může přinést dramatické zvýšení výkonu. Kdy tabulku rozdělit? Záleží to na aplikaci, ale zvážit by se to mělo, když velikost tabulky překročí fyzickou paměť databáze (RAM).
* Partitions mohou být dále definované jako partitions.
* Foreign data. Možnost připojit nějaký externí systém k PostgreSQL a přes něj získávat data pomocí klasických SQL dotazů.
* Příklad: aplikace pro správu počasí, k nejnovějším datům přistupuji častěji než k těm historickým.

## 6. Data Manipulation
* RETURNING * je možné použít i pro klauzule UPDATE či DELETE.

## 7. Queries
* CROSS JOIN se použije pro spojení řádek z jedné tabulky se spojí s každým řádkem z druhé tabulky.
* INNER JOIN je default.
* T1 JOIN T2 USING (a,b) znamená ON T1.a = T2.a AND T1.b = T2.b.
* FULL JOIN je jako LEFT a RIGHT dohromady.
* Možno použít VALUES pro vytvoření tabulky v dotazu: FROM (VALUES ('anne', 'smith'), ('bob', 'jones'), ('joe', 'blow')).
* GROUPING SETS (CUBE a ROLLUP) nahrazuje propojení několika GROUP BY (jako kdybychom několik dotazů spojili přes UNION).
* Klauzule DISTINCT ON pro vyfiltrování výsledků, které mají nějaký sloupec duplicitní.
* UNION (spojení dotazů), INTERSECT (průnik), EXCEPT (rozdíl). Klauzule odstraní duplicitní výsledky, pokud je chci nechat, dodá se ALL.
* Řadit výsledky jako ORDER BY a + b, c. Specifikace, kde budou nulové hodnoty pomocí NULLS FIRST and NULLS LAST.
* Common Table Expressions (CTE) jsou velmi silným nástrojem, mohou se hodit třeba pro efektivní přesun dat z jedné tabulky do druhé.
* CTE s rekurzí (WITH RECURSIVE).

## 8. Data types
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

## 9. Functions and Operators
* Operátor <> je standardní SQL notace pro !=.
* NULL::boolean není FALSE, ale UNKNOWN.
* Celočíselné dělení: 5 / 2 → 2, mocniny: 2 ^ 3 → 8.
* Funkce LEFT('abcde', 2) → ab, RIGHT('abcde', 2) → de, PARSE_IDENT(), QUOTE_IDENT(), QUOTE_LITERAL(), STRING_TO_ARRAY(), REGEXP_STRING_TO_ARRAY()
* Klauzule LIKE je case sensitive, ILIKE je case insensitive.
* Klauzule SIMILAR TO je podobná LIKE, obsahuje ale o trochu více možností směrem k POSIX.
* Operátory pro práci s regulárními výrazy: ~, ~*, !~, !~*.
* Funkce to_char() a to_date() slouží pro převod na textovou verzi data nebo pro parsování data.
* Výpočet dnů, které jsou mezi daty: date - date → integer.
* Výpočet věku mezi dvěma daty: age(timestamp, timestamp), výsledek je pak např. 43 years 9 mons 27 days.
* Konstanty current_time, current_date, now().
* Pro extrahování nějaké části z data se použije date_part().
* Funkce date_trunc() odstraní nějakou část data (třeba zaokrouhlí hodiny ap.).
* Funkce make_date() vytvoří datum, zadává se číslo měsíce, dne ap.
* Operátor OVERLAPS zjistí, zda se dva data překrývají.
* Funkce EXTRACT(field FROM source) získá z data různí informace (i číslo týdne ap.).
* Vrácení času v dané časové zóně pomocí timestamp AT TIME ZONE zone.
* Funkce gen_random_uuid() vygeneruje unikátní UUID.


## Zdroje
* https://wiki.postgresql.org/wiki/Don%27t_Do_This#Don.27t_use_money 
