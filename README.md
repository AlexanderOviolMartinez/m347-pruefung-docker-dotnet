# m347-pruefung-docker-dotnet
## ✅ Lernziel 1: YAML & docker-compose
---

### 📌 **Was ist YAML & wofür wird es verwendet?**

| Begriff       | Erklärung                                                                          |
| ------------- | ---------------------------------------------------------------------------------- |
| YAML          | „YAML Ain’t Markup Language“ – eine einfache, menschenlesbare Auszeichnungssprache |
| Verwendet für | Konfigurationen, z. B. in `docker-compose.yml`, GitHub Actions, Kubernetes         |
| Vorteil       | Klarer Aufbau durch Einrückungen, kein Overhead durch Klammern oder Tags           |
| In Docker     | Beschreibt Container, Netzwerke, Umgebungen – als Ersatz für lange CLI-Befehle     |

> ❗ YAML ist **notwendig**, damit du docker-compose verwenden kannst. Du beschreibst damit **Container & deren Zusammenspiel**.

---

### 🧱 **Grund-Syntax von YAML**

```yaml
# Kommentare mit #
schlüssel: wert

liste:
  - eintrag1
  - eintrag2

objekt:
  unterpunkt:
    schlüssel: wert
```

* Kein `=`, kein `;`, keine `{}` → nur **Doppelpunkt** und **Einrückung**
* Einrückung: **immer 2 Leerzeichen**, **keine Tabs**

---

### 📄 Wichtige YAML-Schlüsselwörter in `docker-compose.yml`

| Schlüsselwort    | Beschreibung                                                                  |
| ---------------- | ----------------------------------------------------------------------------- |
| `version`        | Gibt die Version der Compose-Spezifikation an (z. B. `"3.8"`)                 |
| `services`       | Container-Dienste, die gestartet werden sollen                                |
| `image`          | Verwendetes Container-Image (z. B. `mongo:6`)                                 |
| `build`          | Anweisungen, um ein eigenes Image aus einem Dockerfile zu bauen               |
| `context`        | Build-Kontext (z. B. `"."` für aktueller Ordner)                              |
| `dockerfile`     | Name der Dockerfile-Datei                                                     |
| `ports`          | Port-Mapping: `"host:container"`                                              |
| `environment`    | Liste von Umgebungsvariablen (`KEY=value`)                                    |
| `depends_on`     | Gibt an, welche Container zuerst gestartet werden sollen                      |
| `volumes`        | Persistente Daten außerhalb des Containers speichern                          |
| `networks`       | Netzwerk-Konfiguration für Services                                           |
| `command`        | Startbefehl für den Container überschreiben                                   |
| `restart`        | Automatischer Neustart bei Fehlern (`always`, `on-failure`, `unless-stopped`) |
| `container_name` | Benutzerdefinierter Name des Containers                                       |

---

### 🧪 Beispiel `docker-compose.yml`

```yaml
# Gibt die verwendete Compose-Datei-Spezifikation an (Version 3.8 ist modern und stabil)
version: '3.8'

# Beginnt die Definition aller Containerdienste
services:

  # Erster Dienst mit dem Namen "web" (unsere .NET Web-API)
  web:

    # Baue das Image aus einem lokalen Dockerfile
    build:
      context: .              # Verzeichnis mit dem Quellcode und Dockerfile (aktuelles Verzeichnis)
      dockerfile: Dockerfile # Name der Dockerfile-Datei, die verwendet werden soll

    # Mappe Port 5000 des Hosts auf Port 80 im Container
    ports:
      - "5000:80"             # localhost:5000 → Container-Port 80

    # Setze Umgebungsvariable, damit ASP.NET Core auf Port 80 lauscht
    environment:
      - ASPNETCORE_URLS=http://+:80

    # Warte mit dem Start, bis der "db"-Container läuft
    depends_on:
      - db

  # Zweiter Dienst: MongoDB-Datenbank
  db:
    image: mongo:6            # Verwende das offizielle MongoDB-Image, Version 6

    # Mappe MongoDB-Port vom Container auf denselben Port des Hosts
    ports:
      - "27017:27017"         # localhost:27017 → Container-Port 27017

    # Erstelle ein Volume für persistente Speicherung (damit Daten beim Neustart erhalten bleiben)
    volumes:
      - dbdata:/data/db       # Mountet das Volume "dbdata" in den Container unter /data/db

# Volumes werden außerhalb der Container gespeichert (z. B. unter /var/lib/docker/volumes)
volumes:
  dbdata:                     # Definiert ein Volume mit dem Namen "dbdata"

```
