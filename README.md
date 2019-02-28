# Reporting and Visualization for Service Level Objectives

Our server side Service Level Indicators (SLIs) are tracked using New Relic:

https://alerts.newrelic.com/accounts/100796/policies/351201

The data from New Relic is visualized using the ELK (Elasticsearch, Logstash, Kibana) stack from Elastic.

![](https://newrelic.com/assets/newrelic/source/NewRelic-logo-bug.svg) ![](https://images.contentstack.io/v3/assets/bltefdd0b53724fa2ce/blt9a6279ac82c4aac5/5c11ebcf5b046c520d3f7506/logo-elastic-stack-lt.svg)

- **Elasticsearch**: Store monitoring data
- **Logstash**: Query New Relic HTTP API for SLI data and send to elasticsearch
- **Kibana**: Analytics and visualization 

## Getting Started

### Prerequisites
- Docker
- Docker-compose

### Launch the stack

        cd elastic
        docker-compose up -d

### Open Kibana
        open 'http://localhost:5601/app/kibana#/dashboards'

### Tear down stack

        docker-compose down

## Other commands

### View container logs

        docker-compose logs -f elasticsearch
        docker-compose logs -f logstash
        docker-compose logs -f kibana

### Load Kibana Dashboard template

        cd config/kibana_objects/
        curl -XPOST 'localhost:5601/api/kibana/dashboards/import?force=true' -H "Content-Type: application/json" -H "kbn-xsrf: true" -d @violations_dashboard.json

### Export Kibana Dashboard as template

        # Get dashboard IDs
        curl -s 'localhost:5601/api/saved_objects/_find?type=dashboard' | jq '.saved_objects[] | {id: .id, title: .attributes.title}'

        # Export to a file
        curl 'localhost:5601/api/kibana/dashboards/export?dashboard=<ID_HERE>' | jq . > <FILENAME_HERE>.json