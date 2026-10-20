# Ciociaria: correzioni di visibilità e collisioni

Questa copia parte da `main`, commit `13629216173c84c53024b7f80ee27648f00efff2` del repository lollotonietti/tour-in-ciociaria-prova-mia. Cambia il solo codice del gioco in `guida.html`; gli altri file sono inclusi per provarlo come sito statico.

## Modifiche

- Movimento dell'auto suddiviso in avanzamenti di massimo 35 cm e risoluzione ripetuta dei contatti su tre sezioni della carrozzeria. La risposta rimuove la velocità verso l'ostacolo, aggiunge un rimbalzo e attenua la velocità.
- Il filtro delle collisioni usa le dimensioni di ogni edificio, segmento o isola, anziché escludere qualunque ostacolo oltre 14 metri dal centro. Gli edifici dotati di quote non bloccano l'auto su un diverso livello stradale.
- Cordoli rialzati di 22 cm nelle zone urbane su strade ordinarie: superficie visibile e segmento di collisione vengono creati insieme. Non vengono aggiunti su rampe, ponti, gallerie o attraverso le intersezioni rilevate. Le banchine di terra aperte non diventano muri invisibili. Le dimensioni dei cordoli sono una scelta del gioco, non un rilievo reale.
- Terreno a maglia di 10 metri, con una fascia di rispetto sotto il piano stradale. La guida fuori strada e la pavimentazione dei centri usano gli stessi triangoli del terreno visualizzato.
- La telecamera esterna controlla tutto il percorso dall'auto alla posizione desiderata e si avvicina quando trova un ostacolo. Il piano di taglio vicino è ridotto. L'inquadratura esterna di default mantiene verticali gli edifici anche nell'immagine; guardando volontariamente in alto/basso resta la normale prospettiva.
- Gli edifici OSM prima resi come blocchi semplici usano il generatore di facciate dettagliate già presente: basamenti, cornici, porte e tetti. Gli assi orizzontali sono normalizzati; le pareti restano verticali e la base resta orizzontale, con fondazioni sotto il terreno.

## Da dove viene la mappa

I dati sono incorporati in blocchi JSON dentro `guida.html`. Non viene scaricata una mappa aggiornata da Google Maps durante la guida.

| Blocco/funzione | Dato e impiego |
|---|---|
| `netData`, `netLoad()` | Rete stradale attribuita a OpenStreetMap: 4.357 nodi, 5.294 archi, nomi e attributi. Il gioco la converte in una rete navigabile e superfici 3D. |
| `demData`, `demLoad()`, `demH()` | Quote attribuite nel codice a Copernicus GLO-90. Questa copia contiene griglie ricampionate con passo 150 e 600 metri; il terreno viene interpolato e adattato alle strade. Una maglia grafica di 10 metri non aggiunge misurazioni altimetriche reali. |
| `bldData`, `bldInit()`, `bldPlace()` | 263.434 record edilizi compressi, attribuiti a OSM. Ogni record contiene posizione, orientamento, semidimensioni e informazioni compatte su tipo/piani: la rappresentazione è un rettangolo orientato, non la sagoma completa di ciascun fabbricato. Altezze assenti vengono stimate. |
| `luData`, `luInit()`, `luTrees()` | Uso del suolo OSM su celle di 50 metri: bosco, campi, uliveti ecc. I singoli alberi e cespugli sono generati, non alberi rilevati uno per uno. |
| `bldFill()` | Aggiunge case plausibili nelle aree residenziali e nei centri dove i dati disponibili non bastano. Queste case non sono edifici realmente identificati. |
| `scData` | 316 luoghi culturali, testi e itinerari. I contenuti aggiuntivi arrivano separatamente da `schede.json`. |
| Modelli dedicati e texture canvas | Monumenti selezionati, facciate e finestre sono ricostruzioni programmate; non fotografie georeferenziate degli edifici. |

Il mondo è costruito in tasselli di 400 metri attorno all'auto. La rete stradale non implica una riproduzione completa dell'urbanistica reale: geometria semplificata, larghezze convenzionali, quote interpolate, altezze stimate e riempimento procedurale ne limitano la fedeltà. Il repository non include una procedura completa e riproducibile di acquisizione/aggiornamento dei dataset originali.

Per migliorare la fedeltà occorrerebbe conservare i poligoni completi degli edifici OSM, gli attributi `height` e `building:levels` quando presenti, dati altimetrici più fini e quote affidabili di ponti/gallerie. I monumenti richiedono modelli dedicati. Cambiare solo luci e texture non rende esatta l'urbanistica.

## Verifiche e limiti

Prova in Microsoft Edge headless con WebGL software: caricamento dei dati edifici e uso del suolo, avvio del gioco senza eccezioni JavaScript, contatto con edificio largo, esclusione di un edificio su altro livello, collisione contro barriera sottile a 100 m/s e verticalità delle facciate generate. Nei 2.975 campioni stradali verificati entro 900 metri dalla partenza a Frosinone il terreno non supera la strada. Controllata anche la schermata di guida diurna.

Non è una certificazione di ogni strada, incrocio e ponte della provincia. La maggiore densità del terreno e il maggior dettaglio delle facciate hanno un costo grafico: va verificata la fluidità sui telefoni destinati agli studenti. I cordoli nuovi sono una rappresentazione funzionale nelle aree urbane, non una mappatura reale di tutti i marciapiedi.

## Come provarla

Servire la cartella con un server HTTP locale o un hosting statico; aprire `guida.html`. Evitare l'apertura diretta `file://`, che può impedire richieste JSON e altre funzioni del browser. Provare prima in un'anteprima separata. Per applicare la modifica al progetto esistente basta aggiornare `guida.html`; conservare i contenuti e le foto pubblicate del progetto.
