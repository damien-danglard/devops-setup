# Guide de Monitoring et Observabilité

Ce guide couvre les pratiques essentielles pour assurer la visibilité et la fiabilité de vos systèmes.

## Table des Matières
- [Les Trois Piliers de l'Observabilité](#les-trois-piliers-de-lobservabilité)
- [Métriques](#métriques)
- [Logging](#logging)
- [Tracing](#tracing)
- [Alerting](#alerting)
- [SRE et SLO/SLA](#sre-et-slosla)

## Les Trois Piliers de l'Observabilité

### 1. Métriques (Metrics)
Données numériques agrégées dans le temps
- Performance
- Utilisation des ressources
- Compteurs d'événements

### 2. Logs
Enregistrements détaillés d'événements
- Debugging
- Audit
- Analyse post-mortem

### 3. Traces
Suivi des requêtes à travers les systèmes
- Performance end-to-end
- Détection de bottlenecks
- Compréhension des dépendances

## Métriques

### Golden Signals (4 Signaux d'Or)

#### 1. Latency
Temps de réponse des requêtes

```yaml
Métriques:
  - request_duration_seconds
  - p50, p95, p99 latency
  
Alertes:
  - p95 > 500ms pendant 5min
  - p99 > 2s pendant 5min
```

#### 2. Traffic
Volume de requêtes

```yaml
Métriques:
  - requests_per_second
  - concurrent_connections
  
Alertes:
  - traffic drop > 50% pendant 5min
  - traffic spike > 200% pendant 5min
```

#### 3. Errors
Taux d'erreur

```yaml
Métriques:
  - error_rate (%)
  - errors by type (4xx, 5xx)
  
Alertes:
  - error_rate > 5% pendant 5min
  - 5xx_errors > 1% pendant 5min
```

#### 4. Saturation
Utilisation des ressources

```yaml
Métriques:
  - cpu_utilization (%)
  - memory_utilization (%)
  - disk_utilization (%)
  - network_bandwidth
  
Alertes:
  - cpu > 80% pendant 10min
  - memory > 85% pendant 10min
  - disk > 85% pendant 5min
```

### USE Method (Resources)

**Pour chaque ressource:**

**Utilization:** % de temps occupé
```
- CPU: cpu_usage_percent
- Memory: memory_usage_percent
- Disk: disk_io_percent
- Network: bandwidth_usage_percent
```

**Saturation:** Charge en attente
```
- CPU: load_average
- Memory: swap_usage
- Disk: io_queue_depth
- Network: dropped_packets
```

**Errors:** Compteur d'erreurs
```
- CPU: thermal_throttling_events
- Memory: oom_kills
- Disk: disk_errors
- Network: network_errors
```

### RED Method (Services)

**Rate:** Requêtes par seconde
```prometheus
rate(http_requests_total[5m])
```

**Errors:** Taux d'erreur
```prometheus
rate(http_requests_total{status=~"5.."}[5m]) 
/ 
rate(http_requests_total[5m])
```

**Duration:** Distribution des latences
```prometheus
histogram_quantile(0.95, 
  rate(http_request_duration_seconds_bucket[5m]))
```

### Instrumentation du Code

#### Prometheus (Pull-based)

**Node.js:**
```javascript
const promClient = require('prom-client');

// Compteur
const httpRequestsTotal = new promClient.Counter({
  name: 'http_requests_total',
  help: 'Total HTTP requests',
  labelNames: ['method', 'status', 'path']
});

// Histogramme
const httpRequestDuration = new promClient.Histogram({
  name: 'http_request_duration_seconds',
  help: 'HTTP request duration',
  labelNames: ['method', 'path'],
  buckets: [0.1, 0.5, 1, 2, 5]
});

// Jauge
const activeConnections = new promClient.Gauge({
  name: 'active_connections',
  help: 'Number of active connections'
});

// Middleware Express
app.use((req, res, next) => {
  const start = Date.now();
  
  res.on('finish', () => {
    const duration = (Date.now() - start) / 1000;
    
    httpRequestsTotal.inc({
      method: req.method,
      status: res.statusCode,
      path: req.route?.path || 'unknown'
    });
    
    httpRequestDuration.observe({
      method: req.method,
      path: req.route?.path || 'unknown'
    }, duration);
  });
  
  next();
});

// Endpoint métriques
app.get('/metrics', async (req, res) => {
  res.set('Content-Type', promClient.register.contentType);
  res.end(await promClient.register.metrics());
});
```

**Python (Flask):**
```python
from prometheus_client import Counter, Histogram, Gauge, generate_latest
from flask import Flask, Response
import time

app = Flask(__name__)

# Métriques
http_requests_total = Counter(
    'http_requests_total',
    'Total HTTP requests',
    ['method', 'status', 'path']
)

http_request_duration = Histogram(
    'http_request_duration_seconds',
    'HTTP request duration',
    ['method', 'path'],
    buckets=[0.1, 0.5, 1, 2, 5]
)

# Middleware
@app.before_request
def before_request():
    request.start_time = time.time()

@app.after_request
def after_request(response):
    duration = time.time() - request.start_time
    
    http_requests_total.labels(
        method=request.method,
        status=response.status_code,
        path=request.path
    ).inc()
    
    http_request_duration.labels(
        method=request.method,
        path=request.path
    ).observe(duration)
    
    return response

# Endpoint métriques
@app.route('/metrics')
def metrics():
    return Response(generate_latest(), mimetype='text/plain')
```

### Dashboards Grafana

**Dashboard Application:**
```json
{
  "dashboard": {
    "title": "Application Overview",
    "panels": [
      {
        "title": "Request Rate",
        "targets": [{
          "expr": "rate(http_requests_total[5m])"
        }]
      },
      {
        "title": "Error Rate",
        "targets": [{
          "expr": "rate(http_requests_total{status=~'5..'}[5m])"
        }]
      },
      {
        "title": "P95 Latency",
        "targets": [{
          "expr": "histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))"
        }]
      },
      {
        "title": "CPU Usage",
        "targets": [{
          "expr": "avg(rate(container_cpu_usage_seconds_total[5m])) by (pod)"
        }]
      }
    ]
  }
}
```

## Logging

### Niveaux de Log

```
TRACE   - Très détaillé, debug profond
DEBUG   - Informations de debugging
INFO    - Informations générales
WARN    - Avertissements
ERROR   - Erreurs
FATAL   - Erreurs critiques
```

### Structured Logging (JSON)

**Node.js (Winston):**
```javascript
const winston = require('winston');

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()
  ),
  defaultMeta: {
    service: 'my-app',
    environment: process.env.NODE_ENV
  },
  transports: [
    new winston.transports.Console(),
    new winston.transports.File({ 
      filename: 'error.log', 
      level: 'error' 
    })
  ]
});

// Usage
logger.info('User logged in', {
  userId: '123',
  username: 'john',
  ip: '192.168.1.1'
});

logger.error('Payment failed', {
  userId: '123',
  amount: 99.99,
  error: err.message,
  stack: err.stack
});
```

**Python (structlog):**
```python
import structlog

# Configuration
structlog.configure(
    processors=[
        structlog.stdlib.add_log_level,
        structlog.stdlib.add_logger_name,
        structlog.processors.TimeStamper(fmt="iso"),
        structlog.processors.StackInfoRenderer(),
        structlog.processors.format_exc_info,
        structlog.processors.JSONRenderer()
    ],
    wrapper_class=structlog.stdlib.BoundLogger,
    context_class=dict,
    logger_factory=structlog.stdlib.LoggerFactory(),
)

logger = structlog.get_logger()

# Usage
logger.info("user_logged_in", 
           user_id="123", 
           username="john",
           ip="192.168.1.1")

logger.error("payment_failed",
            user_id="123",
            amount=99.99,
            error=str(e))
```

### Bonnes Pratiques Logging

**✅ À faire:**
```javascript
// Contexte riche
logger.info('Order created', {
  orderId: order.id,
  userId: user.id,
  amount: order.total,
  items: order.items.length
});

// Erreurs avec stack trace
logger.error('Database error', {
  error: err.message,
  stack: err.stack,
  query: sqlQuery,
  params: sqlParams
});

// Corrélation IDs
logger.info('Processing request', {
  correlationId: req.headers['x-correlation-id'],
  userId: req.user.id
});
```

**❌ À éviter:**
```javascript
// Logs sans contexte
logger.info('Success');

// Données sensibles
logger.info('User logged in', {
  password: user.password  // ❌
});

// Logs excessifs
for (let item of items) {
  logger.debug('Processing item', item);  // ❌ dans une boucle
}
```

### Centralisation des Logs

#### ELK Stack

**Architecture:**
```
Application → Filebeat → Logstash → Elasticsearch → Kibana
```

**Filebeat config:**
```yaml
filebeat.inputs:
- type: log
  enabled: true
  paths:
    - /var/log/app/*.log
  json.keys_under_root: true
  json.add_error_key: true

output.logstash:
  hosts: ["logstash:5044"]

processors:
  - add_host_metadata: ~
  - add_cloud_metadata: ~
```

**Logstash config:**
```ruby
input {
  beats {
    port => 5044
  }
}

filter {
  json {
    source => "message"
  }
  
  date {
    match => ["timestamp", "ISO8601"]
  }
  
  grok {
    match => { "message" => "%{COMBINEDAPACHELOG}" }
  }
}

output {
  elasticsearch {
    hosts => ["elasticsearch:9200"]
    index => "app-logs-%{+YYYY.MM.dd}"
  }
}
```

#### Loki (Alternative lightweight)

**Promtail config:**
```yaml
server:
  http_listen_port: 9080

positions:
  filename: /tmp/positions.yaml

clients:
  - url: http://loki:3100/loki/api/v1/push

scrape_configs:
  - job_name: system
    static_configs:
      - targets:
          - localhost
        labels:
          job: applogs
          __path__: /var/log/app/*.log
```

### Requêtes Logs Utiles

**Elasticsearch:**
```json
// Erreurs des dernières 15 minutes
{
  "query": {
    "bool": {
      "must": [
        { "match": { "level": "ERROR" }},
        { "range": { "@timestamp": { "gte": "now-15m" }}}
      ]
    }
  }
}

// Agrégation par erreur
{
  "aggs": {
    "error_types": {
      "terms": {
        "field": "error.keyword",
        "size": 10
      }
    }
  }
}
```

**Loki (LogQL):**
```
// Logs d'erreur
{app="myapp"} |= "ERROR"

// Rate d'erreurs
rate({app="myapp"} |= "ERROR" [5m])

// Extraction de champs JSON
{app="myapp"} | json | user_id="123"
```

## Tracing

### Distributed Tracing

**Concepts:**
- **Trace:** Requête end-to-end à travers services
- **Span:** Unité de travail (operation)
- **Context:** Metadata propagé entre services

**Architecture:**
```
Client → API Gateway → Service A → Service B → Database
         [Trace ID: abc123]
         [Span 1]  [Span 2]   [Span 3]   [Span 4]
```

### OpenTelemetry

**Node.js:**
```javascript
const { NodeTracerProvider } = require('@opentelemetry/sdk-trace-node');
const { registerInstrumentations } = require('@opentelemetry/instrumentation');
const { HttpInstrumentation } = require('@opentelemetry/instrumentation-http');
const { ExpressInstrumentation } = require('@opentelemetry/instrumentation-express');
const { JaegerExporter } = require('@opentelemetry/exporter-jaeger');

// Setup
const provider = new NodeTracerProvider();

provider.addSpanProcessor(
  new SimpleSpanProcessor(
    new JaegerExporter({
      endpoint: 'http://jaeger:14268/api/traces'
    })
  )
);

provider.register();

registerInstrumentations({
  instrumentations: [
    new HttpInstrumentation(),
    new ExpressInstrumentation(),
  ],
});

// Custom span
const tracer = provider.getTracer('my-app');

app.post('/order', async (req, res) => {
  const span = tracer.startSpan('process_order');
  
  try {
    span.setAttribute('user.id', req.user.id);
    span.setAttribute('order.amount', req.body.amount);
    
    const order = await processOrder(req.body);
    
    span.setStatus({ code: SpanStatusCode.OK });
    res.json(order);
  } catch (err) {
    span.setStatus({ 
      code: SpanStatusCode.ERROR,
      message: err.message 
    });
    span.recordException(err);
    throw err;
  } finally {
    span.end();
  }
});
```

**Python (OpenTelemetry):**
```python
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor
from opentelemetry.exporter.jaeger.thrift import JaegerExporter
from opentelemetry.instrumentation.flask import FlaskInstrumentor

# Setup
trace.set_tracer_provider(TracerProvider())
jaeger_exporter = JaegerExporter(
    agent_host_name="jaeger",
    agent_port=6831,
)
trace.get_tracer_provider().add_span_processor(
    BatchSpanProcessor(jaeger_exporter)
)

app = Flask(__name__)
FlaskInstrumentor().instrument_app(app)

tracer = trace.get_tracer(__name__)

@app.route('/order', methods=['POST'])
def create_order():
    with tracer.start_as_current_span("process_order") as span:
        span.set_attribute("user.id", request.user.id)
        span.set_attribute("order.amount", request.json['amount'])
        
        try:
            order = process_order(request.json)
            return jsonify(order)
        except Exception as e:
            span.record_exception(e)
            span.set_status(Status(StatusCode.ERROR))
            raise
```

## Alerting

### Principes d'Alerting

**Alerter sur:**
- ✅ Symptômes (impact utilisateur)
- ✅ Métriques actionables
- ✅ Seuils pertinents

**Ne pas alerter sur:**
- ❌ Causes (logs spécifiques)
- ❌ Métriques non actionables
- ❌ Faux positifs fréquents

### Configuration Alerting

**Prometheus AlertManager:**
```yaml
groups:
- name: application
  interval: 30s
  rules:
  
  # High error rate
  - alert: HighErrorRate
    expr: |
      (
        rate(http_requests_total{status=~"5.."}[5m]) 
        / 
        rate(http_requests_total[5m])
      ) > 0.05
    for: 5m
    labels:
      severity: critical
    annotations:
      summary: "High error rate detected"
      description: "Error rate is {{ $value | humanizePercentage }}"
      runbook: "https://wiki.company.com/runbooks/high-error-rate"
  
  # High latency
  - alert: HighLatency
    expr: |
      histogram_quantile(0.95,
        rate(http_request_duration_seconds_bucket[5m])
      ) > 1
    for: 10m
    labels:
      severity: warning
    annotations:
      summary: "High latency detected"
      description: "P95 latency is {{ $value }}s"
  
  # Low traffic (possible issue)
  - alert: LowTraffic
    expr: |
      rate(http_requests_total[5m]) < 10
    for: 15m
    labels:
      severity: warning
    annotations:
      summary: "Unusually low traffic"
      description: "Traffic is {{ $value }} req/s"
  
  # Pod down
  - alert: PodDown
    expr: up{job="my-app"} == 0
    for: 5m
    labels:
      severity: critical
    annotations:
      summary: "Pod is down"
      description: "{{ $labels.instance }} is down"
```

**AlertManager routing:**
```yaml
global:
  slack_api_url: 'https://hooks.slack.com/services/XXX'

route:
  group_by: ['alertname', 'cluster']
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 4h
  receiver: 'default'
  
  routes:
  - match:
      severity: critical
    receiver: 'pagerduty'
    continue: true
  
  - match:
      severity: critical
    receiver: 'slack-critical'
  
  - match:
      severity: warning
    receiver: 'slack-warning'

receivers:
- name: 'default'
  slack_configs:
  - channel: '#alerts'
    text: '{{ range .Alerts }}{{ .Annotations.description }}{{ end }}'

- name: 'slack-critical'
  slack_configs:
  - channel: '#alerts-critical'
    title: '{{ .GroupLabels.alertname }}'
    text: '{{ range .Alerts }}{{ .Annotations.description }}{{ end }}'

- name: 'pagerduty'
  pagerduty_configs:
  - service_key: 'YOUR_SERVICE_KEY'
```

### On-Call Best Practices

**Runbooks:**
```markdown
# Runbook: High Error Rate

## Symptômes
- Error rate > 5%
- 5xx responses augmentés

## Impact
- Utilisateurs affectés
- Transactions échouées

## Diagnostic
1. Check Grafana dashboard: [link]
2. Check logs: `{app="myapp"} |= "ERROR"`
3. Check recent deployments

## Actions
1. Si déploiement récent:
   - Rollback: `kubectl rollout undo deployment/app`
2. Si problème DB:
   - Check DB connections
   - Check slow queries
3. Si problème externe:
   - Check third-party status pages

## Escalation
- Si pas résolu en 15min: page @senior-engineer
- Si pas résolu en 30min: page @team-lead
```

## SRE et SLO/SLA

### Définitions

**SLI (Service Level Indicator):**
Métrique mesurée (ex: latency, availability)

**SLO (Service Level Objective):**
Cible interne (ex: 99.9% availability)

**SLA (Service Level Agreement):**
Engagement contractuel (ex: 99.5% availability avec pénalités)

### Error Budget

**Concept:** Marge d'erreur acceptable

```
SLO: 99.9% availability
Error budget: 0.1% = 43 minutes de downtime/mois

Si error budget épuisé:
- Pause sur nouvelles features
- Focus sur fiabilité
```

**Calcul:**
```python
# Availability
uptime = successful_requests / total_requests
error_budget = 1 - slo_target
error_budget_remaining = error_budget - (1 - uptime)

# Exemple
# SLO: 99.9%, Uptime actuel: 99.95%
# Budget: 0.1%, Consommé: 0.05%
# Remaining: 50% du budget
```

### SLOs Recommandés

**API REST:**
```yaml
Availability: 99.9% (3 nines)
Latency P95: < 500ms
Latency P99: < 1s
Error rate: < 0.1%
```

**Service Batch:**
```yaml
Success rate: 99%
Duration P95: < 10min
```

**Base de données:**
```yaml
Availability: 99.95%
Query latency P95: < 100ms
```

## Checklist Monitoring

- [ ] Métriques Golden Signals instrumentées
- [ ] Logs structurés en JSON
- [ ] Centralisation des logs (ELK/Loki)
- [ ] Distributed tracing configuré
- [ ] Dashboards Grafana créés
- [ ] Alertes configurées avec runbooks
- [ ] On-call rotation définie
- [ ] SLO/SLI définis
- [ ] Error budget tracking
- [ ] Post-mortems process
