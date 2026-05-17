# Scalable OpenTelemetry Stack

Dieser Stack bietet eine skalierbare Lösung zum Empfangen, Verarbeiten und Visualisieren von OpenTelemetry-Daten. Er nutzt den OTEL Collector als Gateway, InfluxDB zur Speicherung von Metriken und Grafana für das Monitoring.

## Architektur
- **OTEL Gateway**: Zentraler Endpunkt für OTLP-Daten (gRPC/HTTP). Skalierbar via Docker oder Kubernetes HPA.
- **Test Collector**: Ein separater Container, der Host-Metriken generiert und an das Gateway sendet.
- **InfluxDB**: Zeitreihendatenbank zur Speicherung der Metriken.
- **Grafana**: UI zur Visualisierung der Daten und der sendenden Hosts.

## Lokale Ausführung (Docker)

### Voraussetzungen
- Docker und Docker Compose (v2+)

### Starten
```bash
docker compose up -d
```

### Verifizierung
1. **Logs prüfen**: `docker logs -f otel-gateway`. Sie sollten eingehende Metriken sehen.
2. **Grafana**: Öffnen Sie `http://localhost:3000`.
   - Das Dashboard "OTEL Stack Overview" zeigt eine Liste der verbundenen Hosts.
3. **InfluxDB**: Öffnen Sie `http://localhost:8086` (User: `admin`, Pass: `password123`).

## Kubernetes Deployment (Helm)

### Voraussetzungen
- Kubernetes Cluster (lokal z.B. k3d, minikube oder Cloud)
- Helm 3
- Metrics Server (für HPA)

### Installation
```bash
helm install my-otel-stack ./charts/otel-stack
```

### Skalierung
Das Gateway ist mit einem `HorizontalPodAutoscaler` (HPA) konfiguriert. Es skaliert automatisch zwischen 1 und 10 Replicas basierend auf der CPU-Auslastung.

## Datenbanken für Logs (Diskussion)
Für die Speicherung von Log-Dateien wird **Grafana Loki** empfohlen. Es lässt sich nahtlos in diesen Stack integrieren, indem ein Loki-Exporter im OTEL Gateway hinzugefügt wird. Im Gegensatz zu InfluxDB ist Loki speziell auf Log-Aggregation optimiert.

## Testfälle
1. **End-to-End Flow**: Test Collector -> Gateway -> InfluxDB.
2. **UI Check**: Erscheinen neue Hosts automatisch in der Grafana-Tabelle?
3. **Load Test**: Bei hoher Last auf das Gateway sollten durch den HPA neue Pods gestartet werden.
