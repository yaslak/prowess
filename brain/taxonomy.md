# Brain Taxonomy

Knowledge graph schema for the Brain observability hub.

## Node Types

| type | Description | Key properties |
|---|---|---|
| `service` | A microservice in the ecosystem | name, domain, repo_path, stack |
| `model` | An Eloquent model | name, service, table |
| `route` | An HTTP endpoint | method, path, name, service |
| `event` | A domain event | name, service, payload_shape |
| `job` | A queued job | name, service, queue, timeout |
| `doc` | A documentation entry | title, service, path, content |
| `runbook` | An operational procedure | title, service, path, trigger |
| `person` | A stakeholder or user | name, role |

## Edge Types

| type | Description | From → To |
|---|---|---|
| `owns` | Service owns a model | service → model |
| `dispatches` | Service dispatches an event | service → event |
| `listens_to` | Service listens to an event | service → event |
| `calls` | Service calls another service | service → service |
| `triggers` | Event triggers a job | event → job |
| `documented_by` | Model/route is documented | model/route → doc |
| `resolved_by` | Issue resolved by runbook | event/job → runbook |
| `responsible_for` | Person is responsible for | person → service |

## CSV Format

### nodes.csv

```csv
id,type,name,service,meta
1,service,brain,brain,"{""domain"":""brain.addlocal""}"
2,service,luna,luna,"{""domain"":""luna.addlocal""}"
3,model,User,luna,"{""table"":""users""}"
```

### edges.csv

```csv
id,from_id,to_id,type,meta
1,1,2,calls,"{""protocol"":""http""}"
2,2,3,owns,"{}"
```
