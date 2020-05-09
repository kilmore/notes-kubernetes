# Deployments

kubectl get deployments

kubectl set image deployment/myapp-deployment nginx=nginx:1.9.1

kubectl rollout status deployment/myapp-deployment

kubectl rollout history deployment/myapp-deployment

kubectl rollout undo deployment/myapp-deployment 