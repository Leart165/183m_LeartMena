# EC2-Setup – Grundlage für alle Kompetenznachweise

## A) AWS Learner Lab starten
Das AWS Learner Lab wurde gestartet und die AWS Management Console geöffnet.

---

## B) SSH-Schlüsselpaar lokal generieren
Das SSH-Schlüsselpaar wurde lokal auf dem eigenen Rechner erstellt.

Public Key:

```text
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOf3AysMe/+cQZD7KhSQ8hxgbzV0kRf06Pc8EwyTqwbp m183-Mena-tbz
```

Screenshot hier einfügen:

![Public Key Ausgabe](public/image-ssh-public-key.png)

---

## C) Sicherheitsgruppe erstellen
Die Sicherheitsgruppe `m183-sg` wurde erstellt.

Inbound Rule:

| Type | Protocol | Port | Source |
|---|---|---:|---|
| SSH | TCP | 22 | 194.209.11.12/32 |

Screenshot hier einfügen:

![Security Group Inbound Rules](public/image-security-group.png)

---

## D) EC2-Instanz mit Cloud-Init-Script starten
Die EC2-Instanz wurde mit Ubuntu Server 24.04 LTS gestartet. Docker wird über Cloud-Init automatisch installiert.

Cloud-Init-Script:

```yaml
#cloud-config

users:
  - name: ubuntu
    groups: docker
    sudo: ALL=(ALL) NOPASSWD:ALL
    shell: /bin/bash
    ssh_authorized_keys:
      - ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOf3AysMe/+cQZD7KhSQ8hxgbzV0kRf06Pc8EwyTqwbp m183-Mena-tbz

packages:
  - docker.io

runcmd:
  - systemctl start docker
  - systemctl enable docker
```

---

## E) SSH-Verbindung herstellen und Docker prüfen
Die Verbindung zur Ubuntu-Instanz wurde per SSH hergestellt. Docker wurde erfolgreich geprüft.

Verwendete Befehle:

```bash
ssh -i ~/.ssh/m183_mena_key ubuntu@<PUBLIC-IP>
docker --version
docker run hello-world
```

Ergebnis:

```text
Docker version 29.5.3
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

Screenshot hier einfügen:

![SSH Verbindung und Docker Test](public/image-docker-test.png)

---

## F) Instanz stoppen oder terminieren
Während der Arbeit wird die Instanz gestoppt. Nach der Abgabe kann sie terminiert werden.

---

# Leitfragen / Checkpoints

## Public Key und Private Key
Der Public Key darf auf Servern hinterlegt werden. Der Private Key bleibt nur lokal auf dem eigenen Rechner. Mit dem Private Key kann man beweisen, dass man zum hinterlegten Public Key gehört.

## Warum darf der Private Key nie weitergegeben werden?
Wer den Private Key besitzt, kann sich als diese Person per SSH anmelden. Deshalb darf er nie geteilt oder hochgeladen werden.

## Was ist ein Cloud-Init-Script?
Ein Cloud-Init-Script wird beim ersten Start der EC2-Instanz automatisch ausgeführt. In diesem Auftrag installiert es Docker und trägt den SSH Public Key für den Benutzer `ubuntu` ein.

## Warum ist `0.0.0.0/0` für SSH ein Risiko?
`0.0.0.0/0` bedeutet, dass sich das gesamte Internet mit Port 22 verbinden darf. Sicherer ist nur die eigene IP-Adresse, zum Beispiel `194.209.11.12/32`.

## EC2 starten, stoppen und terminieren
Eine EC2-Instanz kann über die AWS Console gestartet, gestoppt oder terminiert werden. Beim Stoppen bleiben die Daten erhalten. Beim Terminieren wird die Instanz gelöscht.

## Unterschied zwischen Stop und Terminate
Bei `Stop` wird die Instanz ausgeschaltet, bleibt aber erhalten. Bei `Terminate` wird die Instanz dauerhaft gelöscht.

## SSH-Verbindung mit eigenem Schlüssel
Die Verbindung erfolgt mit dem lokal generierten Private Key:

```bash
ssh -i ~/.ssh/m183_mena_key ubuntu@<PUBLIC-IP>
```

## Docker verifizieren
Docker wurde mit `docker --version` und `docker run hello-world` geprüft. Die Ausgabe `Hello from Docker!` zeigt, dass Docker korrekt funktioniert.
