# Quando ci vediamo?

Sondaggio sulle date, pagina singola, aperta a chiunque abbia il link: niente account, niente registrazione, basta il nome.

Periodo: 21 novembre – 21 dicembre 2026.

## Com'è fatto

Un solo file, `index.html`, servito da GitHub Pages. I dati stanno su Supabase (progetto `sondaggi`, tabella `public.disponibilita`) e la pagina ci parla direttamente via PostgREST, senza backend in mezzo.

Ogni browser genera un token casuale che tiene in `localStorage`. Il token viaggia nell'header `x-token` sulle modifiche, e le policy RLS lo confrontano con quello della riga: così ognuno può correggere o cancellare solo la propria risposta.

## Sulla chiave nel codice

La chiave Supabase qui dentro è la **publishable key**, pensata per stare in pagine pubbliche. Da sola non apre niente: i permessi sono dati colonna per colonna e filtrati da RLS.

- `select` solo su `id, sondaggio, nome, giorni, created_at, updated_at` — la colonna `token` non è leggibile da nessuno
- `insert` consentito, con un limite di 200 risposte per sondaggio
- `update` e `delete` solo sulla riga il cui token coincide con l'header
- la colonna `token` non è scrivibile dopo l'inserimento

Il progetto Supabase è separato da ogni altro: contiene solo questa tabella.

## Riusarlo per un altro sondaggio

Cambia `SONDAGGIO`, `FROM` e `TO` in testa allo script, e il titolo. La tabella regge più sondaggi in parallelo: le righe sono separate dalla colonna `sondaggio`.
