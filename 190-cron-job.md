### Kubernetes CronJob

#### What is a CronJob?
- Runs Jobs on a predefined recurring schedule.
- Uses standard cron syntax (5 fields).
- Common use cases: backups, cleanup, reports, scheduled API calls.

#### Execution hierarchy

```
CronJob → Job → Pod → Container
```

- CronJob: Defines the schedule.
- Job: Manages task execution.
- Pod: Runs the container.

#### Concurrency policies

| Policy            | Behaviour                                  |
| ----------------- | ------------------------------------------ |
| `Allow` (default) | Allows concurrent Jobs                     |
| `Forbid`          | Skips execution if previous Job is running |
| `Replace`         | Terminates previous Job and starts new one |

#### Example YAML

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: backup-cronjob
spec:
  schedule: "0 2 * * *"       # Cron expression: Every day at 2 AM
  timeZone: "Asia/Kolkata"    # Time zone for scheduling

  concurrencyPolicy: Forbid   # Allow | Forbid | Replace
  startingDeadlineSeconds: 60 # Maximum delay allowed for missed execution

  successfulJobsHistoryLimit: 3 # Retain last 3 successful Jobs
  failedJobsHistoryLimit: 1     # Retain last 1 failed Job

  suspend: false              # Temporarily suspend future executions

  jobTemplate:                # Template used to create Jobs
    spec:
      template:
        spec:
          containers:
            - name: backup
              image: busybox
              command: ["sh", "-c", "echo Running backup"]
          restartPolicy: OnFailure
```

#### Useful commands

```powershell
kubectl apply -f cronjob.yaml
kubectl get cronjobs
kubectl describe cronjob backup-cronjob
kubectl get jobs
kubectl get pods
kubectl logs job/<job-name>
kubectl delete cronjob backup-cronjob
```

