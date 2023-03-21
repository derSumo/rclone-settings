# Vorinstalliern muss sein
> rclone (bitte nicht von der REPO, sondern direkt das neuste)

> fuse

> MergeFS https://github.com/trapexit/mergerfs/releases/

# rclone-settings
Rclone-Service für Google Drive! Diese Lösung wurde speziell entwickelt, um die API-Beschränkungen
von Google maximal nutzen zu können ohne Gebannt zu werden, die bei vielen Anfragen auftreten können. 
Mit meinem Rclone Mount-Service können Sie einen langen Cache nutzen und bei Erreichen einer bestimmten 
GB-Grenze vorhandene Daten überschreiben. Dadurch ist ein perfektes Streaming-Erlebnis mit Plex/Emby garantiert, ohne dass das API-Limit von Google überschritten wird.

Um sicherzustellen, dass der lokale und der Cloud-Dateipfad denselben Systempfad haben (z.B. /Media/Local/Movie + /Media/Remote/Movie = /Media/Remote/Movie), 
nutzen wir MergerFS. Dadurch wird vermieden, dass Systeme wie Sonarr/Plex die Datei neu einscannen müssen,
wenn sie von lokalen auf Remote-Speicher hochgeladen werden. 

Dies spart Traffic und hat den zusätzlichen Vorteil,
dass beim Medienscan auf lokaler Ebene keine API- oder Traffic-Nutzung erforderlich ist und somit alles vorbereitet ist,
bevor Remote-Scans durchgeführt werden müssen.


Unsere Dienste gdrive.service und gdrivemerge.service sind so konfiguriert,
dass gdrive.service erst gestartet wird, wenn eine Netzwerkverbindung hergestellt wurde,
und gdrivemerge.service erst gestartet wird, wenn gdrive.service gestartet wurde.
Dadurch werden Fehler vermieden, die dazu führen könnten, dass leer Ordner erstellt werden und sich Fuse mit MergeFS nicht mehr mouten lässt.


# Upload Script
Dieses Script wird alle 24 Stunden per Cron-Job ausgeführt und ist so konzipiert,
dass es mit einem TPS von 1 arbeitet, um Passiv ohne viele API-Anfragen im Hintergrund zu laufen.
Es verwendet den Parameter --delete-empty-src-dirs, um leere Ordner nach dem Upload zu löschen und Speicherplatz freizugeben.

Bitte editieren Sie den LOCAL= Pfad mit Ihrem lokalen Media-Pfad und ggf. RCLONE_CONFIG=, wenn Sie Ihre rclone.config nicht mit root erstellt haben.
Sie können den Parameter --min-age (WERT) verwenden, um anzugeben, wie alt eine Datei mindestens sein muss, bevor sie verschoben wird.
Wir empfehlen eine Einstellung von 48 Stunden, wenn Sie bei Plex z.B. Thumbnail-Generierung aktiviert haben und ein Zeitfenster in der Mitternacht haben,
in dem die Generierung startet. Wenn Sie nur wenig Speicherplatz zur Verfügung haben, reicht oft auch 1 Stunde.

Das Upload-Limit bei Google beträgt 750 GB. Wir haben den Befehl --max-transfer auf 500 GB eingestellt.
Bei Bedarf können Sie diesen Befehl anpassen und die gewünschte GB-Zahl angeben oder "--drive-stop-on-upload-limit" verwenden,
um das Limit beim Hoster automatisch zu erkennen und das Uploaden zu stoppen, wenn das Limit erreicht ist.
