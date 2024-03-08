# Wiederholung Tag 1

## Patterns / Pradigmen

1. Idempotent, Exactly-Once, At least once, at most once, 
- Gibt an, was bei einem erneuten Versuch im Fehlerfall passiert.
- Keine synchrone Kommunikation 
- Wird im Consumer implementiert.

2. Publish / Subscribe
- Wird von Kafka umgesetzt. Die Consumer entsprechend dem Subscriber, die Producer dem Publisher

3. Active Polling

- Publisher stellen Daten zur Verfügung, Subscriber / Consumer muss die Daten selbstständig abrufen.
- Bei einem Consumer: Rückmeldung muss erfolgen. Nach einem Timeout: Fehler. Consumer gilt als gestört, Last wird neu verteilt. Verhindern: Verarbeitungszeit der Daten begrenzen. Aufwändige Verarbeitung in einem eigenen Thread.

4. Event Sourcing

- Nicht der Zustand sondern die Historie wird gespeichert (z.B. bei git)
- Kann mit Kafka umgesetzt
- Ggf. viele Daten, z.B. Compaction

## Komponenten von Kafka

1. Broker

- Server im Cluster, einziger Server
- Speicher für die Messages, Kommunikation, d.h. empfängt Messages und leitet sie weiter.
- Broker verwaltet Topics
- Wird vom Betrieb verwaltet, Entwickler/innen bekommen Zugangsdaten
- Bootstrap-Server: Konfigurations-Parameter in vielen APIs, bezeichnet einige Broker im Cluster für den Verbindungsaufbau. Mindestens einer muss erreichbar sein. Es müssen nicht alle Broker im Cluster angegeben werden.

2. Consumer

- Holt die Nachrichten (Active Polling)
- Kann offsets commiten. D.h. das Speichern des eigenen Consumer-Offsets. 
- Mögliche Speicher für Offsets: Topic, lokal, externe Systeme
- Beim Speichern der Offsets in einem Topic: manuell (d.h. programmatisch), default: alle 5 Sekunden im Hintergrund.
- Single-Threaded: Mehrere Theads => Mehrere Consumer
- Ist immer Teil einer Consumer-Group

3. Consumer-Group
- Property / Pflichtfeld für die Consumer-API: Name oder ID. (Häufig: Name der Anwendung oder Name des Systems.)
- Skalierung für Consumer: Teilen sich Daten eines Topics auf.
- #Consumer <=  #Partions 
- Reblancing: Neu-Aufteilung der Partionen auf die Consumer. Wenn:
  - Neuer Consumer tritt einer Gruppe bei.
  - Consumer verlässt die Gruppe

4. Producer

- Produziert die Nachrichten (Publisher)
- Kümmert sich die Partitionierung / Sharding
  Default: Gruppierung nach Key (z.B. Hash modulo #partitions) / Round-Robin (key ist null)
 - Multi-Threading möglich / thread-safe

 5. Topic 
 - "Kanal" im Publish & Subscribe
 - Gruppiert Nachrichten (logisch), Adresse 
 - Unterschieden sich in der Konfiguration:
   - Anzahl der Partionen
   - Cleanup, (delete, compact)
   - Segment Size (ms, bytes): 1. Segment: Head, liegt auch RAM, weitere Segmente: Tail: Nur auf der Festplatte 
- Wird von den Entwickler/innen verwaltet. Konfiguration ist in der Regel Teil der Deployment Artefakte.

6. Partition

- Technischer Bestandteil eines Topics
- Kann auf verschiedenen Broker gespeichert
- Ermöglicht die Skalierung über verschiedene Broker oder verschiedene Consumer ein Gruppe
- Offset: Eindeutige Position einer Nachricht in einer Partition. Schreiben neuer Nachricht: Append-only, d.h. der offset wächst.
- Kann repliziert werden: Ausfallsicherheit

7. Message

- Key-Value-Pair (Record: KeyValue + MetaDaten), Bytes
- Hilfreich: Gleiches Datenformat in einem Topic
- Hat einen Header mit technischen Informationen wie timestamp
- Maximale Größe (default): 1 MiB. 

