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
---
Perfekt! 👇
Wir gehen jetzt **Lernziel 2** durch:

> **„Sie kennen die Syntax von `docker-compose.yml` und können die Schlüsselwörter korrekt anwenden“**
> (laut [Lehrmittel M347](https://gbssg.gitlab.io/m347/docker-compose/docker-compose-einsetzen/#docker-compose-datei))

---

## ✅ Lernziel 2: Syntax & Schlüsselwörter von `docker-compose.yml`

---

### 📌 **Was ist `docker-compose.yml` und wofür ist sie da?**

➡️ **Wurde bereits in Lernziel 1 erklärt** – kurz gesagt:

* Dient dazu, mehrere Container (Services) mit einer einzigen Datei zu definieren und zu starten.
* Anstatt lange `docker run`-Befehle zu tippen, **beschreibst du alles in YAML**.

> 📚 Quelle:
> [https://gbssg.gitlab.io/m347/docker-compose/docker-compose-einsetzen/#docker-compose-datei](https://gbssg.gitlab.io/m347/docker-compose/docker-compose-einsetzen/#docker-compose-datei)

---

### 📄 **Struktur & Syntax der `docker-compose.yml` – laut Lehrmittel & Praxis**

---

#### 🔧 Basisstruktur:

```yaml
version: '3.8'
services:
  <servicename>:
    image: <image-name>
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "hostPort:containerPort"
    environment:
      - VARIABLENNAME=Wert
    depends_on:
      - anderer-service
    volumes:
      - ./lokal:/container
```

---

### 🧾 Erklärung wichtiger Schlüsselwörter:

| Schlüsselwort    | Bedeutung                                                                 |
| ---------------- | ------------------------------------------------------------------------- |
| `version`        | Gibt an, welche Compose-Version verwendet wird (z. B. `'3.8'`)            |
| `services`       | Block, in dem alle Container (Dienste) definiert sind                     |
| `<servicename>`  | Der Name des Dienstes (z. B. `web`, `mongo`, `db`)                        |
| `image`          | Docker-Image, das verwendet wird (z. B. `mongo:6`)                        |
| `build`          | Bauanweisung für eigenes Image (anstelle von `image`)                     |
| `context`        | Verzeichnis, das als Build-Kontext verwendet wird (meist `.`)             |
| `dockerfile`     | Pfad zur Dockerfile (optional, falls nicht `Dockerfile` heißt)            |
| `ports`          | Verbindet Port vom Host mit Container (z. B. `"5000:80"`)                 |
| `environment`    | Setzt Umgebungsvariablen für den Container (`VAR=wert`)                   |
| `depends_on`     | Gibt an, welche Services zuerst starten sollen                            |
| `volumes`        | Externe Speicherung außerhalb des Containers                              |
| `networks`       | Definiert Netzwerke (optional, bei komplexen Setups)                      |
| `restart`        | Gibt an, ob Container bei Absturz neu starten (`always`, `on-failure`, …) |
| `command`        | Startbefehl des Containers überschreiben                                  |
| `container_name` | Benutzerdefinierter Container-Name (anstatt automatisch generiertem)      |

---

### ✅ Syntax-Regeln (laut YAML-Spezifikation)

| Regel                            | Beispiel                   |
| -------------------------------- | -------------------------- |
| Einrückung mit **2 Leerzeichen** | `key:\n  unterkey: wert`   |
| Listen mit `-` beginnen          | `ports:\n  - "5000:80"`    |
| Kommentare mit `#`               | `# Dies ist ein Kommentar` |
| Keine Tabulatoren (`\t`)         | Nur Leerzeichen erlaubt    |

---

## ✅ Lernziel 3: `docker compose` Befehle und Optionen

---

### 📌 Was macht `docker compose`?

* `docker compose` liest die Datei `docker-compose.yml`
* Startet, konfiguriert und verbindet mehrere Container auf einmal
* Spart Zeit und erspart lange `docker run`-Befehle
* Verwaltert Volumes, Netzwerke, Images etc. automatisch

---

### 🔧 Wichtigste Befehle im Überblick

| Befehl                      | Bedeutung                                                          |
| --------------------------- | ------------------------------------------------------------------ |
| `docker compose up`         | Startet alle Container gemäß `docker-compose.yml`                  |
| `docker compose down`       | Stoppt und entfernt alle gestarteten Container                     |
| `docker compose build`      | Baut die Container neu, gemäß Dockerfile                           |
| `docker compose ps`         | Zeigt laufende Container der aktuellen `docker-compose.yml` an     |
| `docker compose logs`       | Zeigt die Logs aller Container an                                  |
| `docker compose exec <srv>` | Führt einen Befehl in einem laufenden Container aus (z. B. `bash`) |

---

### 🧪 Zusatzoptionen (`-d`, `--build`, etc.)

| Option             | Bedeutung                                                                       |
| ------------------ | ------------------------------------------------------------------------------- |
| `-d`               | *Detached mode* – Container laufen im Hintergrund                               |
| `--build`          | Erzwingt beim `up` das **Neu-Bauen** der Images, auch wenn sie schon existieren |
| `--no-cache`       | Baut Images **ohne Verwendung des Caches** (immer komplett neu)                 |
| `--remove-orphans` | Entfernt Container, die nicht mehr in der `docker-compose.yml` stehen           |
| `--force-recreate` | Erzwingt das **Neuerstellen** aller Container, auch ohne Änderungen             |

---

### 🧾 Beispiel-Ablauf

#### Schritt 1 – Container starten:

```bash
docker compose up --build -d
```

| Teil      | Bedeutung                                         |
| --------- | ------------------------------------------------- |
| `up`      | Starte alle Container                             |
| `--build` | Baue alle Images vorher neu (nach Dockerfile)     |
| `-d`      | Starte im Hintergrund (nicht im aktiven Terminal) |

---

#### Schritt 2 – Container stoppen:

```bash
docker compose down
```

| Teil   | Bedeutung                                                 |
| ------ | --------------------------------------------------------- |
| `down` | Stoppe und lösche alle Container aus diesem Compose-Stack |

---

#### Optional: Komplett neu starten

```bash
docker compose down --volumes
docker compose build --no-cache
docker compose up -d
```

---

### 💡 Typische Anwendungsfälle

| Ziel                                 | Befehl                                                |
| ------------------------------------ | ----------------------------------------------------- |
| Schnell Container starten            | `docker compose up -d`                                |
| Änderungen an Code übernehmen        | `docker compose up --build -d`                        |
| Probleme lösen (neu bauen & starten) | `docker compose down && docker compose up --build -d` |

---




