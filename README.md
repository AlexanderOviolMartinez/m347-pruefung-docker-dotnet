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
version: '3.8'

services:
  web:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "5000:80"
    environment:
      - ASPNETCORE_URLS=http://+:80
    depends_on:
      - db

  db:
    image: mongo:6
    ports:
      - "27017:27017"
    volumes:
      - dbdata:/data/db

volumes:
  dbdata:
```
