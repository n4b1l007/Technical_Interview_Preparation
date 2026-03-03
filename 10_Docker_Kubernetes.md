# Docker & Kubernetes

### Q1: Docker কী এবং কেন ব্যবহার করা হয়?

**A:** Docker একটি containerization platform যা application ও তার dependency একটি isolated container-এ package করে। এতে "আমার machine-এ কাজ করে কিন্তু তোমার machine-এ করে না" সমস্যা দূর হয়।

```bash
# Image build করো
docker build -t myapp:1.0 .

# Container চালাও
docker run -d -p 8080:80 myapp:1.0
```

### Q2: Container vs Virtual Machine (VM)?

**A:**
- **VM:** পুরো OS চালায়, ভারী, boot নিতে সময় লাগে।
- **Container:** Host OS-এর kernel শেয়ার করে, হালকা, milliseconds-এ start হয়।

```
VM:        [App] [Libs] [Guest OS] → Hypervisor → Host OS
Container: [App] [Libs] → Docker Engine → Host OS (shared kernel)
```

### Q3: Dockerfile-এর মূল instructions কী কী?

**A:**

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:8.0   # base image
WORKDIR /app                                # working directory
COPY . .                                    # ফাইল copy করো
RUN dotnet build                            # build time কমান্ড
EXPOSE 80                                   # port expose করো
ENTRYPOINT ["dotnet", "MyApp.dll"]          # container start হলে এটি চলবে
```

### Q4: Docker Compose কী?

**A:** একাধিক container একসাথে define ও run করার tool। একটি `docker-compose.yml` file-এ সব service, network ও volume configure করা যায়।

```yaml
services:
  api:
    build: .
    ports:
      - "8080:80"
    depends_on:
      - db
  db:
    image: postgres:15
    environment:
      POSTGRES_PASSWORD: secret
```

```bash
docker-compose up -d   # সব service start করো
docker-compose down    # সব বন্ধ করো
```

### Q5: Docker image vs Docker container?

**A:**
- **Image:** Read-only template — blueprint-এর মতো।
- **Container:** Image-এর running instance — image থেকে তৈরি live process।

```bash
docker images          # সব image দেখো
docker ps              # চলমান container দেখো
docker ps -a           # সব container (বন্ধও) দেখো
```

### Q6: Kubernetes (K8s) কী?

**A:** Kubernetes একটি container orchestration platform যা বহু container-এর deployment, scaling ও management automate করে।

> **সহজ কথায়:** Docker একটি container চালায়, Kubernetes হাজার হাজার container পরিচালনা করে।

### Q7: Kubernetes-এর মূল components কী কী?

**A:**
- **Pod:** সবচেয়ে ছোট unit — এক বা তার বেশি container ধারণ করে।
- **Deployment:** Pod-এর desired state manage করে, rollout ও rollback করে।
- **Service:** Pod-গুলোর সামনে stable network endpoint দেয়।
- **Ingress:** বাইরে থেকে HTTP/HTTPS traffic route করে।
- **ConfigMap / Secret:** Configuration ও sensitive data আলাদা রাখে।

```yaml
# Deployment উদাহরণ
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 3          # ৩টি Pod চালাও
  selector:
    matchLabels:
      app: myapp
  template:
    spec:
      containers:
        - name: myapp
          image: myapp:1.0
          ports:
            - containerPort: 80
```

### Q8: Kubernetes-এ Scaling কীভাবে কাজ করে?

**A:** Manual বা automatic (HPA) উভয়ভাবে scale করা যায়।

```bash
# Manual scaling
kubectl scale deployment myapp --replicas=5

# HPA — CPU usage ৭০% ছাড়ালে auto scale করবে
kubectl autoscale deployment myapp --cpu-percent=70 --min=2 --max=10
```

### Q9: Rolling Update vs Recreate strategy?

**A:**
- **Rolling Update:** পুরানো Pod একটি একটি করে নতুন দিয়ে replace করে — **zero downtime**।
- **Recreate:** আগে সব পুরানো Pod বন্ধ করে, তারপর নতুন চালু করে — downtime হয়।

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 1   # কতটি Pod একসাথে বন্ধ থাকতে পারবে
    maxSurge: 1         # কতটি extra Pod চলতে পারবে
```

### Q10: Liveness Probe vs Readiness Probe?

**A:**
- **Liveness Probe:** Container জীবিত আছে কিনা check করে। ব্যর্থ হলে restart করে।
- **Readiness Probe:** Container traffic নেওয়ার জন্য ready কিনা check করে। ব্যর্থ হলে Service থেকে সরিয়ে দেয় (restart করে না)।

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 80
  initialDelaySeconds: 10
  periodSeconds: 5

readinessProbe:
  httpGet:
    path: /ready
    port: 80
  initialDelaySeconds: 5
  periodSeconds: 3
```


---

**[Next: 11_Microservices.md →](11_Microservices.md)**
