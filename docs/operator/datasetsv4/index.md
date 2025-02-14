# How to run new dataset endpoint in v4

For now one can run and play with the new version of dataset endpoints v4. Here is how to set it up:


1. Voraussetzungen: Adminrechte, Git, Docker, NodeJS (ich habe es jetzt
mit v.20.18.2 laufen) inkl. npm (bei mir v10.8.2).
2. Wenn Frontend laufen soll: Über npm angular installieren via "npm
install -g @angular".
3. Front- und Backend Repositories von GitHub klonen.
4. Jeweils im Verzeichnis von Backend und Frontend einmal "npm install"
um die Dependencies zu installieren.
5. Backendkonfiguration: Das Backend braucht fünf zusätzliche Dateien um
richtig zu laufen: "loggers.json", "functionalAccounts.json",
"proposalTypes.json", "datasetTypes.json" und ".env". Für alle davon ist
eine Beispieldatei schon vorhanden (die .env.example ist allerdings
Mist, ich habe mal meine momentane angehängt mit der es läuft).
6. MongoDB starten: Mache ich über einen Docker-Container. Rezept: Image
bitnami/mongodb:latest, Volumen "mongodb" anlegen und im Container auf
/bitnami/mongodb mounten, Port 27017 im Container auf 27017 im Host
öffnen.
7. (Optional?) MongoDB anlegen: Über Docker in den laufenden mongodb
Container einloggen. Auf der Shell "mongosh dacat" ausführen, das legt
die DB "dacat" an (andere name geht auch, dann muss das nur auch in der
.env anders!). Laut der alten Dokumentation muss man dann in der mongosh
den Befehl "db.Dataset.createIndex( { "$**" : "text" } )" ausführen, um
Textindexing zu bekommen. Nicht 100% sicher ob das noch aktuell ist,
musst du mal Max zu fragen.
8. Backend starten: Im Backendverzeichnis "npm run start", sollte dann
nach ca. 10s laufen. Testen, indem "localhost:3000/explorer" im Browser
geöffnet wird - wenn Swagger zu sehen ist, läuft es.
9. Frontend starten: Nachdem das Backend läuft, wieder "npm run start"
im Frontendverzeichnis ausführen. Frontend sollte nach Kompilierung (ca.
30s) auf "localhost:4200" zu sehen sein.