# ELK-Stack
ELK-Stack für RNR

Installation des ELK-Stack (Elasticsearch, Kibana, Logstash) im Windows-Umfeld.


1.	Installation des Java SE Development Kit 8. 
	http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html

2.	Installation der einzelnen Komponenten.
Elasticsearch - Kibana – Logstash
	https://www.elastic.co/downloads
Verwenden des nssm um die Komponenten als Dienste zu Starten.
	https://nssm.cc/
Installationspfad z.B.: C:\ELK-Stack\…

Oder über den Windows-Installer (v5.5.0.24)
	https://github.com/gigi81/elk-windows-installer/releases

3.1	Verschieben der Logstash-Konfigurations-Files in den logstash\bin Ordner (pro benötigte Pipe eine Datei).

	Logstash-Konfigurationsdateien sind:
	- logstash-AuthLog.conf
	- logstash-EWSTracerRnR.conf
	- logstash-LogfilesRnR.conf

3.2	Anfälliges Anpassen der Konfigurationsdateien.

Ein Konfigurations-File besteht mindestens aus einem Input und einem Output zusätzlich werden Filter angegeben.

Im Input wird der Pfad angegeben, in dem sich die entsprechenden Log-Files befinden, mit denen gearbeitet werden soll.

Im Filter werden um die Logs gefiltert und ein parsing durchgeführt, um die Logdateien entsprechend zu modellieren. 

Eine grosse Hilfe beim Parsen ist der Grok-Constructor.
	http://grokconstructor.appspot.com/

Der Output sollte in unserem Fall auf die ElasticSerach DB zeigen.

4.	Starten von Elasticsearch + Kibana über den Task-Manager (falls diese nicht bereits Laufen)

5.1	Damit die Logstash-Pipes parallel laufen können, muss ein Pfad angegeben werden in dem jede Pipe allfällige Logdaten ablegen kann.
dazu erstellt man einen im Logstash-Verzeichnis einen Temp-Ordner mit je einem Unterordner Pro verwendete Pipe.

5.2	Starten der Logstash-Pipes über das CMD. 

CMD-Befehle:

cd C:\ELK-Stack\logstash\bin

logstash -f ./logstash-LogfilesRnR.conf --config.reload.automatic --path.data C:\ELK-Stack\logstash\temp\MultipipeStore\ApiPipeStore
logstash -f ./logstash-EWSTracerRnR.conf --config.reload.automatic --path.data C:\ELK-Stack\logstash\temp\MultipipeStore\EwsTracerPipeStore
logstash -f ./logstash-AuthLog.conf --config.reload.automatic --path.data C:\ELK-Stack\logstash\temp\MultipipeStore\AuthPipeStore


6.	Über http://localhost:9200/logstash-* sollten alle vorhandenen Daten, welche mittels Logstash übertragen wurden, angezeigt werden.

7.	Über cURL-Commands kann ElasticSearch angepasst werden. --> Dazu muss die cURL-Erweiterung für CMD installiert werden.
	z.B. - 
	C:\curl -XDELETE localhost:9200/Logstash-*


8.	Nun kann über http://localhost:5601 Kibana aufgerufen werden, um die Daten weiter aufzubereiten.

9. Wenn Kibana gestartet ist, kann man über Management/Saved Objects - und den <Import> Button bestehende Konfigurationen, Dashboards, Suchen und Visualisierungen hinzufügen.
