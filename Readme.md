### CI/CD Workflow

### Running the system

- docker-compose up --build

### Testing the system

- ./task_dispatcher.sh

### Check Logs

- docker exec -it [worker-node-1|worker-node-2] bash
- cat job_results.log

https://github.com/user-attachments/assets/e8e6d010-5372-4026-93ff-17f271ad7a84


### Minikube Deployment

1. Start Minikube with ARM64 Support

- minikube start --driver=docker
- minikube addons enable storage-provisioner

2. Restart minikube for a fresh install in case of errors

- minikube stop
- minikube delete
- minikube start --driver=docker
- minikube addons enable storage-provisioner

3. Verify Your Kubernetes version

- minikube version
- kubectl version

4. Use Minikube's Docker Daemon

- eval $(minikube docker-env)

5. Minikube Dashboard

- minikube dashboard

6. Build Docker Images

- docker build -t fastapi-job-manager:latest .
- docker build -t worker-node:latest .

7. Verify docker images loaded in minikube

- minikube ssh -- docker images

8. Note: These are one-time steps that have already been executed to convert
         existing docker-compose file to Kubernetes YAML.

- brew install kompose
- kompose convert

- Edit worker-node deployment files
    - Replace worker-node-1.yaml / worker-node-1.yaml to worker-node.yaml
    - Set Replicas to 3
    - Set imagePullPolicy: Never in both worker-node and fastapi-job-manager to prevent pulling image from registry
                           and fetch images built locally instead.

- Move docker-compose.yaml to docker-compose.yaml.bak so that kubernetes does not see it as a deployment file.

9. Deploy Services to Minikube

- kubectl apply -f .

10. Expose the FastAPI service to your local machine by port forwarding

- kubectl port-forward service/fastapi-job-manager 8000:8000

11. Submit Jobs

- ./task_dispatcher.sh

12. Check Job Manager Logs

- kubectl get pods
- kubectl logs [fastapi-job-manager]

13. Open an Interactive Shell in the Worker Pod

- kubectl get pods
- kubectl exec -it [worker-node] -- /bin/bash

14. Check Worker Logs

- cat job_results.log

15. Cleanup Resources

- kubectl delete pods --all
- kubectl delete svc --all
- kubectl delete deployment --all

https://github.com/user-attachments/assets/b4c9c532-dac5-44e8-aced-bad0a247a9ce
