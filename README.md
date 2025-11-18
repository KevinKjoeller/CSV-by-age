# CSV-by-age
# CSV Age Filter

Program til at filtrere personer efter alder fra en CSV-fil (comma-separated values).

## Beskrivelse

Dette program læser en CSV-fil med navne og aldre, og filtrerer kun de rækker hvor personen er yngre end eller lig med en angiven maksimal alder. Programmet håndterer fejl i input-data og rapporterer dem til stderr uden at inkludere dem i output-filen.

## Funktionalitet

- Læser CSV-filer linje for linje
- Parser navn og alder fra hver linje
- Filtrerer baseret på maksimal alder
- Håndterer tre typer fejl:
  - **Blank linje**: Advarer og springer over
  - **Manglende alder**: Ingen komma eller anden kolonne
  - **Ugyldigt aldersformat**: Alder kan ikke konverteres til tal
- Fejlmeddelelser skrives til stderr (ikke i output-filen)
- Understøtter både filer og stdin/stdout

## Kompilering

```powershell
clang CSV_age.c -o CSV_age.exe
```

Alternative compilere:
```powershell
gcc CSV_age.c -o CSV_age.exe
cl CSV_age.c
```

## Brug

### Grundlæggende syntax

```powershell
./CSV_age.exe max-age [input-file] [output-file]
```

### Eksempler

**Filtrer med input-fil og output-fil:**
```powershell
./CSV_age.exe 17 people-with-age.csv output.csv
```

**Filtrer med input-fil, output til stdout:**
```powershell
./CSV_age.exe 10 people-with-age.csv
```

**Input fra stdin, output til stdout:**
```powershell
./CSV_age.exe 54
```

**Med pipe i PowerShell:**
```powershell
echo "Anna, 12" | ./CSV_age.exe 15
```

**Med flere linjer:**
```powershell
@"
Nuvi Våle, 18
Lumio Satō, 29
Veski Ruañ, 12
"@ | ./CSV_age.exe 15
```

## Input format

CSV-filen skal have formatet:
```
Navn, Alder
```

**Eksempel (`people-with-age.csv`):**
```
Nuvi Våle, 18

Aeral Körn
Lumio Satō, 29
Veski Ruañ, 12
```

## Output

Programmet udskriver kun gyldige linjer der opfylder alderskravet:

```
Veski Ruañ, 12
```

**Fejlmeddelelser (til stderr):**
```
Line 2: Blank line
Line 3: Age missing
```

## Fejlhåndtering

| Fejltype | Beskrivelse | Handling |
|----------|-------------|----------|
| Blank linje | Tom linje i input | Advarsel til stderr, fortsæt |
| Alder mangler | Ingen anden kolonne efter komma | Fejl til stderr, spring over |
| Ugyldig alder | Alder kan ikke konverteres til tal | Fejl til stderr, spring over |
| Fil kan ikke åbnes | Input/output fil findes ikke eller ingen adgang | Afslut program med fejl |

## Kodestruktur

### Hovedfunktioner

- **`filter_stream(FILE *istream, FILE *ostream)`**
  - Læser input stream linje for linje
  - Parser navn og alder med `strtok()`
  - Validerer og filtrerer baseret på alder
  - Skriver gyldige linjer til output stream

- **`main(int argc, char *argv[])`**
  - Parser kommandolinje-argumenter
  - Åbner input/output streams (filer eller stdin/stdout)
  - Kalder `filter_stream()`
  - Lukker filer

### Konstanter

- `LINE_MAX`: 100 - Maksimal linjelængde
- `DELIM`: "," - CSV delimiter (komma)

## Tekniske detaljer

- Bruger `fgets()` til at læse linjer
- Bruger `strtok()` til at opdele navn og alder
- Bruger `sscanf()` til at konvertere alder-string til tal
- Fejlmeddelelser sendes til `stderr` med `fprintf(stderr, ...)`
- Output sendes til `stdout` eller fil med `fprintf(ostream, ...)`

## Begrænsninger

- Maksimal linjelængde: 100 tegn
- Forventer præcis to kolonner: navn og alder
- Delimiter skal være komma (`,`)
- Understøtter ikke CSV med quoted fields eller embedded commas

## Eksempel på fuld arbejdsgang

1. Opret test-fil `people-with-age.csv`:
```
Anna Smith, 12
Bob Jones, 25
Charlie Brown, 8
Diana Prince, 45
```

2. Kompiler programmet:
```powershell
clang CSV_age.c -o CSV_age.exe
```

3. Filtrer for alder ≤ 15:
```powershell
./CSV_age.exe 15 people-with-age.csv filtered.csv
```

4. Se resultatet:
```powershell
cat filtered.csv
```
Output:
```
Anna Smith, 12
Charlie Brown, 8
```

## License

Til uddannelsesformål.
