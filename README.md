## Piano di riorganizzazione dell'infrastruttura dei dati statici di CIMAFoundation.

Questo documento propone un piano di riorganizzazione della gestione e della fornitura dei dati statici in uso nei diversi progetti e attività di CIMAFoundation.
La proposta è la sintesi di una serie di analisi e valutazioni fatte nell'ambito IT e nasce dall'urgenza, non più prorogabile, di risolvere situazioni inerenti infrastrutture hardware e software obsolete, non più mantenibili e spesso con problemi operativi.

Attualmente i dati statici sono erogati (nei formati WMS, WCS, WFS) da 4 differenti servizi:

#### "GEOSERVER del CIMA" 
(https://geoserver.cimafoundation.org/geoserver), installato sulla macchina 172.16.104.11 con distribuzione Ubuntu 12.04.5:

il servizio espone 3.022 Layers (raster e vettoriali), la grande maggioranza dei dati risiedono a filesystem, ma vi sono anche store che puntano a servizi PostGis, per i dati vettoriali;
la macchina e la distribuzione sono obsolete, parte dei dati non sono più in uso perché relativi a progetti terminati e andrebbero quindi eliminati;

#### "GEOSERVER associato al GEONODE del CIMA"
(https://geodata.cimafoundation.org/geoserver), installato sulla macchina 172.16.104.21, con distribuzione Ubuntu 12.04.5:

il servizio espone 1.803 Layers (raster e vettoriali), i dati raster risiedono a filesystem, i dati vettoriali sono archiviati in un servizio PostGis;
la macchina e la distribuzione sono obsolete, parte dei dati non sono più in uso perché relativi a progetti terminati e andrebbero quindi eliminati.

#### "GEOSERVER associato al GEONODE di ERRA"
(https://erradramgeonode.mydewetra.org/geoservergeonode), installato sulla macchina virtuale 10.82.70.79, che risiede in sala server, con distribuzione Ubuntu 16.04:

il servizio espone 647 Layers (raster e vettoriali), i dati raster risiedono a filesystem, i dati vettoriali sono archiviati in un servizioPostGis;

#### "nuovo GEOSERVER associato al GEONODE su cloud"
(https://geonode.cimafoundation.org/geoserver) installato su cloud AWS:

il servizio espone 117 Layers (raster e vettoriali), i dati raster risiedono a filesystem, i dati vettoriali sono archiviati in un servizioPostGis;

L'idea centrale della riorganizzazione è quella di utilizzare per la distribuzione del dato principalmente GEOSERVER, vista la robustezza del servizio, il mantenimento del progetto e la possibilità di aggiornamento del software compatibile con l'infrastruttura esistente. La proposta prevede la pubblicazione di un servizio unico di GEOSERVER su cloud, nel quale far confluire tutti i dati attualmente presenti nelle 4 linee sopra citate.
Di contro, vista la più difficile gestione del servizio GEONODE/GEOSERVER, l'impossibilità di aggiornare il software a versioni più recenti in modo automatico e l'utilizzo macchinoso da parte degli operatori (considerazione di Guido), si è pensato di aggiornare (potenziare) e mantenere il "nuovo GEOSERVER associato al GEONODE su cloud" per i soli progetti che necessitano, in modo esplicito, di un catalogo di archiviazione del dato.
In quest'ottica, cercheremo di rendere l'operazione di esportazione di tutti i dati sopra citati e la conseguente importazione nel "nuovo GEOSERVER" il più automatizzata possibile.
Si è inoltre valutato di archiviare tutto il dato sorgente (raster e vettoriale) a filesystem, per rendere l'infrastruttura il più possibile semplice, abbandonando conseguentemente il servizio PostGis. Per quest'ultimo passaggio si dovranno implementare degli script di esportazione del dato da PostGis e conseguente ripubblicazione in GEOSERVER con le corrette associazioni di stilizzazione.

In ultimo, ma non meno importante, vorremmo introdurre per la fornitura dei futuri dati di tipo raster l'utilizzo della tecnologia Cloud optimized GeoTIFF (COG) (https://github.com/cogeotiff/cog-spec/blob/master/spec.md). Si tratta di una soluzione moderna ma già consolidata, che garantisce flessibilità, scalabilità e migliori prestazioni. Naturalmente ciò comporta l'adeguamento di tutti i nostri client (dewetra, ecc.) al supporto di questa tecnologia e per questo, in fase di transizione, si è valutato di continuare ad utilizzare GEOSERVER (come servizio WMS), ma pubblicando un dato sorgente di tipo COG (https://docs.geoserver.org/latest/en/user/community/cog/cog.html). Questa soluzione consentirebbe in futuro, una volta aggiornati i client, di ottenere il dato direttamente come COG.
