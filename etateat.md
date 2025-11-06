 

**目录**

[5002.1 总体路线（优先级 & 概览）](#5002.1 总体路线（优先级 & 概览）)

[5002.2 下面按步骤展开，包含具体命令与文件示例](#5002.2 下面按步骤展开，包含具体命令与文件示例)

[5002.2.1 把代码迁移到 GitLab](#5002.2.1 把代码迁移到 GitLab)

[5002.2.2 选择 CI：建议优先用 GitLab CI（集成、Runner、变更触发）](#5002.2.2 选择 CI：建议优先用 GitLab CI（集成、Runner、变更触发）)

[5002.2.3 容器化（Dockerfile 样例）](#5002.2.3 容器化（Dockerfile 样例）)

[5002.2.4 搭建镜像仓库：Harbor vs 阿里云镜像服务（ACR）](#5002.2.4 搭建镜像仓库：Harbor vs 阿里云镜像服务（ACR）)

[5002.2.5 CI/CD：GitLab CI 示例（构建、扫描、推镜像、部署 k8s）](#5002.2.5 CI%2FCD：GitLab CI 示例（构建、扫描、推镜像、部署 k8s）)

[5002.2.6 容器编排：在单台服务器上如何做 k8s？](#5002.2.6 容器编排：在单台服务器上如何做 k8s？)

[5002.2.7 MySQL / Redis 在 k8s 上如何部署？](#5002.2.7 MySQL %2F Redis 在 k8s 上如何部署？)

[5002.2.8 日志集中化与查看（替代现在 tail -f /.../app.log）](#5002.2.8 日志集中化与查看（替代现在 tail -f %2F...%2Fapp.log）)

[5002.2.9 监控与告警（最低限推荐）](#5002.2.9 监控与告警（最低限推荐）)

[5002.2.10 镜像安全扫描、镜像签名与策略](#5002.2.10 镜像安全扫描、镜像签名与策略)

[5002.2.11 Secrets 管理](#5002.2.11 Secrets 管理)

[5002.2.12 备份策略（数据库、镜像、持久数据）](#5002.2.12 备份策略（数据库、镜像、持久数据）)

[5002.2.13 零停机 / 部署策略](#5002.2.13 零停机 %2F 部署策略)

[5002.2.14 日常运维 & 运行管理建议（现在 tail 的替代体系）](#5002.2.14 日常运维 & 运行管理建议（现在 tail 的替代体系）)

[5002.3 逐步实施计划（时间轴 / 每步关键命令）](#5002.3 逐步实施计划（时间轴 %2F 每步关键命令）)

[5002.3.1 阶段 0（现在，天）：](#5002.3.1 阶段 0（现在，天）：)

[5002.3.2 阶段 1（1 周） — 容器化并自动构建镜像：](#5002.3.2 阶段 1（1 周） — 容器化并自动构建镜像：)

[5002.3.3 阶段 2（第2周） — 部署轻量 k8s（k3s）并上应用：](#5002.3.3 阶段 2（第2周） — 部署轻量 k8s（k3s）并上应用：)

[5002.3.4 阶段 3（第3周） — 日志与监控基础：](#5002.3.4 阶段 3（第3周） — 日志与监控基础：)

[5002.3.5 阶段 4（第4周） — 数据库/备份/HA）：](#5002.3.5 阶段 4（第4周） — 数据库%2F备份%2FHA）：)

[5002.3.6 阶段 5（第5-6周） — 安全、扫描、自动化回滚、灰度发布：](#5002.3.6 阶段 5（第5-6周） — 安全、扫描、自动化回滚、灰度发布：)

[5002.4 具体文件/命令清单（拷贝即用）](#5002.4 具体文件%2F命令清单（拷贝即用）)

[5002.5 常见陷阱与注意事项](#5002.5 常见陷阱与注意事项)

[5002.6 把一套可直接复制粘贴到仓库/服务器的文件集合一次性生成出来](#5002.6 把一套可直接复制粘贴到仓库%2F服务器的文件集合一次性生成出来)

[5002.6.1 backend/Dockerfile](#5002.6.1 backend%2FDockerfile)

[5002.6.2 frontend/Dockerfile + frontend/nginx.conf](#5002.6.2 frontend%2FDockerfile %2B frontend%2Fnginx.conf)

[5002.6.3 .gitlab-ci.yml（放在仓库根）](#5002.6.3 .gitlab-ci.yml（放在仓库根）)

[5002.6.4 Helm chart：ops/helm-chart（基础模版）](#5002.6.4 Helm chart：ops%2Fhelm-chart（基础模版）)

[5002.6.5 MySQL StatefulSet + PVC + Backup CronJob](#5002.6.5 MySQL StatefulSet + PVC + Backup CronJob)

[5002.6.6 Loki + Promtail + Grafana 最小清单（快速查看日志）](#5002.6.6 Loki + Promtail + Grafana 最小清单（快速查看日志）)

[5002.6.7 辅助脚本与常用命令片段](#5002.6.7 辅助脚本与常用命令片段)

[5002.6.8 替换 / 配置提示（务必阅读）](#5002.6.8 替换 %2F 配置提示（务必阅读）)

[5002.6.9 下一步建议（优先级）](#5002.6.9 下一步建议（优先级）)

[5002.7 针对5002.6中文件、配置的完整细化输出](#5002.7 针对5002.6中文件、配置的完整细化输出)

[5002.7.1 A. Helm Chart — production-ready（包含 ConfigMap、Secret、HPA、Ingress TLS、PVC、probes、autoscaling）](#5002.7.1 A. Helm Chart — production-ready（包含 ConfigMap、Secret、HPA、Ingress TLS、PVC、probes、autoscaling）)

[1 ops/helm-chart/Chart.yaml](#1 ops%2Fhelm-chart%2FChart.yaml)

[2 ops/helm-chart/values-prod.yaml](#2 ops%2Fhelm-chart%2Fvalues-prod.yaml)

[3 ops/helm-chart/templates/_helpers.tpl](#3 ops%2Fhelm-chart%2Ftemplates%2F_helpers.tpl)

[4 ops/helm-chart/templates/secrets.yaml](#4 ops%2Fhelm-chart%2Ftemplates%2Fsecrets.yaml)

[说明：可以把敏感值放在 chart 的 files 目录（ops/helm-chart/secrets/...），但生产更推荐使用 SealedSecrets / Vault 并直接在 k8s 集群创建 secret，而不要把密码放到 chart。default 仅为示例。](#说明：可以把敏感值放在 chart 的 files 目录（ops%2Fhelm-chart%2Fsecrets%2F...），但生产更推荐使用 SealedSecrets %2F Vault 并直接在 k8s 集群创建 secret，而不要把密码放到 chart。default 仅为示例。)

[5 ops/helm-chart/templates/configmap.yaml](#5 ops%2Fhelm-chart%2Ftemplates%2Fconfigmap.yaml)

[如果用 Secret 注入敏感值，则在 deployment 中使用 envFrom secretRef 或 env valueFrom。](#如果用 Secret 注入敏感值，则在 deployment 中使用 envFrom secretRef 或 env valueFrom。)

[6 ops/helm-chart/templates/service-backend.yaml](#6 ops%2Fhelm-chart%2Ftemplates%2Fservice-backend.yaml)

[7 ops/helm-chart/templates/service-frontend.yaml](#7 ops%2Fhelm-chart%2Ftemplates%2Fservice-frontend.yaml)

[8 ops/helm-chart/templates/backend-deployment.yaml](#8 ops%2Fhelm-chart%2Ftemplates%2Fbackend-deployment.yaml)

[9 ops/helm-chart/templates/frontend-deployment.yaml](#9 ops%2Fhelm-chart%2Ftemplates%2Ffrontend-deployment.yaml)

[10 ops/helm-chart/templates/hpa.yaml](#10 ops%2Fhelm-chart%2Ftemplates%2Fhpa.yaml)

[11 ops/helm-chart/templates/pvc.yaml](#11 ops%2Fhelm-chart%2Ftemplates%2Fpvc.yaml)

[12 ops/helm-chart/templates/ingress-tls.yaml](#12 ops%2Fhelm-chart%2Ftemplates%2Fingress-tls.yaml)

[13 A 部署 / 测试步骤（简明）](#13 A 部署 %2F 测试步骤（简明）)

[5002.7.2 B. Jenkinsfile（若你坚持用 Jenkins 而不是 GitLab CI）](#5002.7.2 B. Jenkinsfile（若你坚持用 Jenkins 而不是 GitLab CI）)

[5002.7.3 C. Loki/Promtail/Grafana 使用 Helm 部署 + Grafana Dashboard JSON（生产推荐用 Helm）](#5002.7.3 C. Loki%2FPromtail%2FGrafana 使用 Helm 部署 %2B Grafana Dashboard JSON（生产推荐用 Helm）)

[1 Helm 安装（简要命令）](#1 Helm 安装（简要命令）)

[2 Grafana Dashboard JSON（示例简易版 — 保存为 grafana/dashboard-happyshop.json）](#2 Grafana Dashboard JSON（示例简易版 — 保存为 grafana%2Fdashboard-happyshop.json）)

[5002.7.4 D. MySQL 备份并上传到阿里云 OSS（完整脚本 + k8s CronJob 示例 + Secret 配置）](#5002.7.4 D. MySQL 备份并上传到阿里云 OSS（完整脚本 + k8s CronJob 示例 + Secret 配置）)

[1 在 k8s 创建 OSS 凭据 Secret](#1 在 k8s 创建 OSS 凭据 Secret)

[2 CronJob YAML（使用包含 ossutil 的镜像）](#2 CronJob YAML（使用包含 ossutil 的镜像）)

[3 如何自建 oss-backup 镜像（Dockerfile 示例）](#3 如何自建 oss-backup 镜像（Dockerfile 示例）)

[5002.7.5 恢复演练（验证备份）](#5002.7.5 恢复演练（验证备份）)

[5002.7.6 常见运维提示（总结性的快速参考）](#5002.7.6 常见运维提示（总结性的快速参考）)

------



从 现有的单台阿里云服务器（前端 vue 静态由 nginx 托管，后端 jeecgboot + mysql + redis + nginx 反向代理，且已经做了开机自启 / 自检 / 自愈）作为起点，**制定出一个从 版本控制迁移到容器化、CI/CD、镜像仓库、Kubernetes 编排、日志/监控/备份、安全等 一整套可执行、分步化、尽量工程化的路线与具体命令/文件示例。**

步骤既包含**现在马上能做的（单机 / 渐进）**，也包含**走向 k8s / 高可用 / 自动化 的推荐路径**，并给出关键配置样板（Dockerfile、.gitlab-ci.yml、k8s 部署/ingress、日志/监控方案）。

------

# 

# 5002.1 总体路线（优先级 & 概览）

1. **迁移代码到 GitLab（自托管 or GitLab.com）**，并建立分支策略（dev/test/prod）。
2. **选择 CI 工具**：推荐优先用 **GitLab CI**（集成、免额外 Jenkins）——也给出 Jenkins Pipeline 的可替代方案。
3. **把前后端构建成 Docker 镜像**（提供 Dockerfile 样本）。
4. **搭建镜像仓库**：推荐自建 **Harbor**（在你的阿里云服务器或独立 VM 上）或使用阿里云容器镜像服务（ACR）。给出 pros/cons。
5. **在 CI 中自动构建、扫描（Trivy）、推镜像到 Harbor，并在 push 后触发 k8s 部署（kubectl/Helm）**。
6. **决定 k8s 部署形式**：
   - 单台服务器：推荐 **k3s**（轻量）或 Docker + docker-compose；也可以用 kubeadm（更“原生”但复杂）。
   - 生产/可扩展：强烈推荐使用阿里云托管 Kubernetes（ACK）或多虚拟机集群。
7. **日志/监控/告警/追踪**：部署 Prometheus + Grafana（metrics）、EFK 或 Loki+Grafana（日志）、Alertmanager、可选 Jaeger（分布式追踪）。
8. **数据库/Redis 管理与备份**：使用 PV/PVC 或外部托管数据库；配置定期备份（mysqldump、xtrabackup、或云盘快照）。
9. **安全/镜像扫描/权限/Secrets 管理/网络策略**。
10. **自动化部署细节、回滚、健康检查、灰度 / 金丝雀**。

------

# 

# 5002.2 下面按步骤展开，包含具体命令与文件示例

## 5002.2.1 把代码迁移到 GitLab

- 在 GitLab 建仓：`happyshop/frontend`、`happyshop/backend`、`ops/helm-charts`（或 monorepo）。
- 本地把 Gitee 仓库推到 GitLab：

```
# 假设已有本地仓库
git remote add gitlab git@gitlab.example.com:yourgroup/happyshop-backend.git
git push -u gitlab --all
git push -u gitlab --tags
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

- 建议：采用 GitFlow 或 trunk-based flow（公司偏好决定）。保护主分支（require MR, CI pass）。

------

## 5002.2.2 选择 CI：建议优先用 GitLab CI（集成、Runner、变更触发）

- 若仍想用 Jenkins（已有经验），也可以：Jenkins 做流水线、GitLab 做代码托管。
- **推荐理由（GitLab CI）**：无需额外 Jenkins 服务器，Runner 可在你的服务器/容器里跑，和 GitLab Registry / Harbor 集成更简单。

**GitLab Runner 安装（Linux）**

```
# 安装并注册 runner（示例简化）
curl -L --output /tmp/gitlab-runner-linux-amd64 \
  https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-amd64
sudo mv /tmp/gitlab-runner-linux-amd64 /usr/local/bin/gitlab-runner
sudo chmod +x /usr/local/bin/gitlab-runner
# 注册时会让你输入 GitLab URL、token、executor（docker/shell），选 docker 推荐
sudo gitlab-runner register
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

------

## 5002.2.3 容器化（Dockerfile 样例）

**1 后端（jeecgboot 是 Spring Boot）Dockerfile（多阶段）**：

```
# backend/Dockerfile
FROM maven:3.8.8-openjdk-17 AS build
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn -B -DskipTests package

FROM eclipse-temurin:17-jdk-jammy
WORKDIR /app
COPY --from=build /app/target/jeecgboot-backend.jar ./app.jar
ENV JAVA_OPTS="-Xms256m -Xmx1g -Dspring.profiles.active=prod"
EXPOSE 8080
CMD java $JAVA_OPTS -jar /app/app.jar
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

**2 前端（Vue）Dockerfile（构建静态并用 nginx 托管）**：

```
# frontend/Dockerfile
FROM node:18 AS build
WORKDIR /app
COPY package.json yarn.lock ./
RUN yarn install --frozen-lockfile
COPY . .
RUN yarn build

FROM nginx:stable
COPY --from=build /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

在项目根放 `.dockerignore` 避免把不必要文件打到镜像里。

------

## 5002.2.4 搭建镜像仓库：Harbor vs 阿里云镜像服务（ACR）

- **Harbor（自托管）**：优点是可在私有网络里、完整 UI、项目/用户/ROLES、镜像复制到其他 Harbor、支持 Clair/Trivy、可集成LDAP。缺点：资源消耗、需要单独服务器（可与你现有服务器同机但建议独立 VM）。
- **阿里云容器镜像服务（ACR）**：优点托管、稳定、和 ACK 深度集成；缺点成本和权限/出海限制。
- **建议**：生产或长期维护，使用 Harbor 或 ACR 都可；如果你希望完全自控且已经有 spare VM：部署 Harbor；如果想省运维并且计划上 ACK：用阿里 ACR。

如果选择 Harbor（简略安装步骤）：

- 安装 Docker / docker-compose 或 Helm + k8s（Harbor 官方有离线或 Helm chart），最简单单机使用 docker-compose 部署 Harbor。
- 创建项目 `happyshop`，配置 robot account 或用户/LDAP，记住仓库地址：`harbor.example.com/happyshop/jeecg-backend`。

------

## 5002.2.5 CI/CD：GitLab CI 示例（构建、扫描、推镜像、部署 k8s）

下面是 `.gitlab-ci.yml` 的精简版（假设使用 GitLab Runner docker executor）：

```
stages:
  - build
  - scan
  - push
  - deploy

variables:
  IMAGE: harbor.example.com/happyshop/${CI_PROJECT_NAME}:${CI_COMMIT_SHORT_SHA}

build:
  stage: build
  image: docker:24 # for docker-in-docker or use docker:dind service
  services:
    - docker:dind
  script:
    - docker build -t $IMAGE .
  artifacts:
    expire_in: 1h
    paths: []

scan:
  stage: scan
  image: aquasec/trivy:latest
  script:
    - trivy image --exit-code 1 --severity CRITICAL $IMAGE || true
  allow_failure: true

push:
  stage: push
  image: docker:24
  services:
    - docker:dind
  before_script:
    - echo $HARBOR_PASSWORD | docker login harbor.example.com -u $HARBOR_USER --password-stdin
  script:
    - docker push $IMAGE

deploy:
  stage: deploy
  image: bitnami/kubectl:latest
  script:
    - kubectl config set-cluster ...
    - kubectl -n happyshop set image deployment/jeecg-backend jeecg-backend=$IMAGE
  only:
    - main
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

- 用 GitLab CI/CD secret variables 存 `HARBOR_USER`/`HARBOR_PASSWORD`、kubeconfig 或 deploy token。
- 更完善的做法：用 Helm chart + `helm upgrade --install` 来管理 k8s 资源，并在 CI 中执行 `helm`。

------

## 5002.2.6 容器编排：在单台服务器上如何做 k8s？

- **选项 A（单机/轻量）**：`k3s`（Rancher 的轻量 k8s），占用小、快速上手，适合单台或少量节点测试/轻量生产。
- **选项 B（生产/可靠）**：阿里云 ACK（托管）或在多台 VM 上用 `kubeadm` 构建集群。
- **建议**：如果你现在只有一台服务器且资源允许，先用 `k3s` 把前后端、mysql、redis 都容器化并交给 k8s 管理。未来扩容再迁移到 ACK 或多节点 kubeadm 集群。

**k8s 资源样例（Deployment + Service + Ingress）****简要示例（backend）：**

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jeecg-backend
  namespace: happyshop
spec:
  replicas: 2
  selector:
    matchLabels: { app: jeecg-backend }
  template:
    metadata:
      labels: { app: jeecg-backend }
    spec:
      containers:
      - name: jeecg-backend
        image: harbor.example.com/happyshop/jeecg-backend:TAG
        ports: [{ containerPort: 8080 }]
        readinessProbe:
          httpGet: { path: /actuator/health, port: 8080 }
          initialDelaySeconds: 10
          periodSeconds: 10
        livenessProbe:
          httpGet: { path: /actuator/health, port: 8080 }
          initialDelaySeconds: 30
          periodSeconds: 30
        resources:
          requests: { cpu: "200m", memory: "512Mi" }
          limits:  { cpu: "1", memory: "1Gi" }
---
apiVersion: v1
kind: Service
metadata: { name: jeecg-backend, namespace: happyshop }
spec:
  selector: { app: jeecg-backend }
  ports: [{ port: 8080, targetPort: 8080 }]
  type: ClusterIP
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

**Ingress（nginx-ingress）**：

- 部署 nginx-ingress controller（在 k3s 中通常内置或可用 helm）。
- 创建 Ingress 资源把域名指向 service。

------

## 5002.2.7 MySQL / Redis 在 k8s 上如何部署？

- **选项**：
  - 在 k8s 部署 MySQL StatefulSet + PVC（适合熟练管理）。
  - 或者使用 **外部托管 DB（阿里 RDS）**：更可靠、备份/高可用简单，推荐用于生产。
- **单机临时方案**：把现有 MySQL 换成容器并使用 hostPath PV / local-path-provisioner，但注意 single-point-of-failure。

**简单的 MySQL PVC 示例**（不完全）：

```
apiVersion: apps/v1
kind: StatefulSet
metadata: { name: mysql, namespace: happyshop }
spec:
  serviceName: mysql
  replicas: 1
  selector:
    matchLabels: { app: mysql }
  template:
    metadata: { labels: { app: mysql } }
    spec:
      containers:
      - name: mysql
        image: mysql:8
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom: { secretKeyRef: { name: mysql-secret, key: root-password } }
        ports: [{ containerPort: 3306 }]
        volumeMounts: [{ name: data, mountPath: /var/lib/mysql }]
  volumeClaimTemplates:
  - metadata: { name: data }
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources: { requests: { storage: 20Gi } }
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

------

## 5002.2.8 日志集中化与查看（替代现在 `tail -f /.../app.log`）

- **原则**：容器化后，**不要**再依赖文件路径 tail；把日志输出到 `stdout/stderr`，由容器 runtime / k8s 捕获。
- **方案 A（ELK/EFK）**：Filebeat / Fluentd -> Elasticsearch -> Kibana（或 Fluent Bit -> Elasticsearch -> Kibana）。好处：强大搜索分析；缺点资源消耗大。
- **方案 B（Grafana Loki + Promtail + Grafana）**：比较轻量，查询语法不错，和 Grafana 集成良好。推荐如果你资源有限，先用 Loki。
- **方案 C（外部日志服务）**：Aliyun Log Service（SLS）或其他托管方案。
- **部署建议**：
  - 在 k8s 上部署 `promtail`（或 fluent-bit）收集容器日志，推到 Loki/Elasticsearch。
  - 在 Grafana 或 Kibana 上建立 Dashboard，做告警（比如错误日志高于阈值发邮件/钉钉）。
- **快速查看**：安装 `kubectl logs -f deploy/jeecg-backend -c jeecg-backend` 可查看 Pod 的日志（替代 tail）。也可用 `stern`（多 Pod tail）或 `kail`。

示例：使用 `kubectl logs`：

```
# 查看最近 Pod 的日志
kubectl -n happyshop get pods -l app=jeecg-backend
kubectl -n happyshop logs -f <pod-name> -c jeecg-backend
# 多 Pod 实时 tail，使用 stern（安装后）
stern jeecg-backend -n happyshop
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

------

## 5002.2.9 监控与告警（最低限推荐）

- **Prometheus**（指标抓取） + **Grafana**（可视化） + **Alertmanager**（告警）。
- 启用 `kube-state-metrics`、`node-exporter`、`cAdvisor` 采集节点容器指标。
- 给 jeecg 后端暴露 `/actuator/prometheus` 或 `micrometer` metrics endpoint。
- 在 Grafana 中建立关键面板：CPU/Memory、响应时间、QPS、数据库连接数、错误率。
- 设置 Alertmanager 把告警发到钉钉/企业微信/邮件。

------

## 5002.2.10 镜像安全扫描、镜像签名与策略

- 使用 **Trivy** 做 CI 阶段镜像扫描（演示已包含）。
- 在 Harbor 中开启漏洞扫描（Clair/Trivy 集成）。
- 若更严格：设置 Admission Controller（例如 OPA Gatekeeper）拒绝未签名或包含高危漏洞镜像。

------

## 5002.2.11 Secrets 管理

- 在 k8s 用 `Secrets` 存 DB 密码、第三方密钥；不要把密码写入镜像或 Git。
- 更安全：使用 **SealedSecrets**（加密的 Git-友好 secrets）、或 HashiCorp Vault 集成 k8s。

------

## 5002.2.12 备份策略（数据库、镜像、持久数据）

- **MySQL**：每日全备 + 增量（mysqldump、xtrabackup），并将备份上传到 OSS 或另一个存储。示例 cronjob:

```
0 3 * * * mysqldump -u root -p'PASSWORD' --all-databases > /backup/mysql-$(date +%F).sql && \
  tar czf /backup/mysql-$(date +%F).tar.gz /backup/mysql-$(date +%F).sql && \
  aliyun oss cp /backup/mysql-$(date +%F).tar.gz oss://your-bucket/backup/
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

- **镜像**：Harbor 自带复制/备份策略，定期备份 Harbor 数据库和配置。
- **PV 数据**：定期做快照（阿里云盘支持快照）。

------

## 5002.2.13 零停机 / 部署策略

- 在 k8s 使用 `readinessProbe`、`livenessProbe`，设置 rolling update（默认 k8s 支持）。
- 可以使用 `canary` 或 `blue-green`（通过 ingress 或 service 切流量）实现灰度发布。
- 在 CI 中实现自动回滚策略：部署失败或应用异常时自动回退上一个版本（用 `helm rollback` 或 `kubectl rollout undo`）。

------

## 5002.2.14 日常运维 & 运行管理建议（现在 tail 的替代体系）

- **快速日志**：`kubectl logs` / `stern` / `kubetail`。
- **结构化日志**：应用输出 JSON 日志，便于 Loki/ELK 解析。
- **监控面板**：Grafana dashboards（HTTP latency, error rate, DB connections, CPU/Mem）。
- **告警**：配置 Alertmanager -> 钉钉/企业微信。
- **审计 & 追踪**：Jaeger / OpenTelemetry（若你想跟踪接口链路）。
- **访问控制**：部署 Vault 或 Kubernetes RBAC + NetworkPolicy。

------

# 

# 5002.3 逐步实施计划（时间轴 / 每步关键命令）

> 假设你想把单机逐步改按 k8s 管理，给出 6 周的迭代式路线（可按需加速）

## 5002.3.1 **阶段 0（现在，天）**：

- 将代码 push 到 GitLab，配置分支保护。
- 在 GitLab 配置 CI 变量：HARBOR_*、KUBE_CONFIG（或 deploy token）。
- 在现有服务器上安装 GitLab Runner（docker executor）并注册。

## 5002.3.2 **阶段 1（1 周） — 容器化并自动构建镜像**：

- 写 Dockerfile（前述样本），在本地构建并测试。
- 在 GitLab CI 中实现 build -> push 到 Harbor（先手动登录 Harbor 试推）。
- 在 Harbor 中确认镜像可用。

## 5002.3.3 **阶段 2（第2周） — 部署轻量 k8s（k3s）并上应用**：

- 在服务器安装 k3s（或在独立 VM 上试验）：

  ```
  curl -sfL https://get.k3s.io | sh -
  ```

  ![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

- 在 k3s 上部署 nginx-ingress、cert-manager（如果需要 TLS）。

- 用 kubectl apply 部署 backend/frontend deployment（pull 来自 Harbor 的镜像）。

## 5002.3.4 **阶段 3（第3周） — 日志与监控基础**：

- 部署 Prometheus + Grafana（使用 kube-prometheus-stack Helm chart）。
- 部署 Loki + promtail + Grafana（或 EFK）。
- 配置几个关键 Dashboard。

## 5002.3.5 **阶段 4（第4周） — 数据库/备份/HA）**：

- 决定 MySQL 在 k8s 还是外部 RDS。若在 k8s，部署 StatefulSet + PVC 并配置定期备份到 OSS。
- 配置 Redis（stateful 或外部 ApsaraDB for Redis 更佳）。

## 5002.3.6 **阶段 5（第5-6周） — 安全、扫描、自动化回滚、灰度发布**：

- 在 CI 中加入镜像扫描（Trivy），把结果 feed 回 MR。
- 在 k8s 上加 NetworkPolicy、RBAC、Secrets（SealedSecrets）。
- 优化 Helm chart，CI 做 `helm upgrade --install` 并在失败时回滚。

------

# 

# 5002.4 具体文件/命令清单（拷贝即用）

我已经在上文给出了关键的 Dockerfile、.gitlab-ci.yml、k8s Deployment 示例、MySQL statefulset 示例、k3s 安装命令、常用 kubectl 查看日志命令和备份命令。补充几个实用命令：

- 查看 Pod / logs：

```
kubectl -n happyshop get pods
kubectl -n happyshop describe pod <pod-name>
kubectl -n happyshop logs -f deploy/jeecg-backend
stern jeecg-backend -n happyshop
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

- 回滚：

```
kubectl -n happyshop rollout undo deployment/jeecg-backend
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

- 查看节点资源：

```
kubectl top nodes
kubectl top pods -n happyshop
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

------

# 

# 5002.5 常见陷阱与注意事项

- **单机 k8s 要警惕单点故障**（node down 会导致所有服务不可用）。测试/开发可用，生产建议至少 3 节点或托管 ACK。
- **镜像安全**：别把敏感信息写入镜像（例如 DB 密码）。
- **资源请求/limits**：必须设置，否则调度与 OOM 风险。
- **健康探针**：必须配置，防止 k8s 把尚未启动完成的 Pod 标记为就绪并发流量给它。
- **备份验证**：备份不是万能，必须定期做恢复演练。
- **日志量**：ELK/ES 在日志量大时资源消耗惊人，Loki 更节省资源。

------

# 

# 5002.6 把一套**可直接复制粘贴**到仓库/服务器的文件集合一次性生成出来

**内容覆盖：**

- 后端 / 前端 Dockerfile（多阶段构建）
- 生产级 `.gitlab-ci.yml`（构建、扫描、推送到 Harbor、Helm 部署、回滚）
- 基础 Helm chart（可用于前后端 + config/ingress + secrets）
- k8s 最少化监控/日志（Loki + Promtail + Grafana 的快速清单）
- MySQL StatefulSet + PVC + 备份 CronJob 示例（备份到本地路径/可扩展为 OSS）
- 一些辅助脚本片段与 README 片段（如何替换变量 / secrets）

**把下面文件内容分别复制到项目对应位置（把每个文件名写在标题上），其中 `REPLACE_\*` / `## TODO` 的地方按环境替换（Harbor 地址、域名、secret、kubeconfig、仓库名等）。**

**目录建议（参考）：**

```
repo-root/
├─ backend/
│  ├─ Dockerfile
│  └─ ...
├─ frontend/
│  ├─ Dockerfile
│  └─ nginx.conf
├─ ops/
│  ├─ helm-chart/
│  │  ├─ Chart.yaml
│  │  ├─ values.yaml
│  │  └─ templates/...
│  ├─ k8s-manifests/
│  │  ├─ mysql-statefulset.yaml
│  │  ├─ mysql-backup-cronjob.yaml
│  │  └─ loki-promtail-grafana.yaml
└─ .gitlab-ci.yml
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

------

## `5002.6.1 backend/Dockerfile`

**文件路径：`backend/Dockerfile`**

```
# backend/Dockerfile - Spring Boot (jeecgboot) 多阶段构建
# Build stage
FROM maven:3.8.8-eclipse-temurin-17 AS builder
WORKDIR /workspace
# 复制 maven 配置和依赖描述以利用缓存
COPY pom.xml mvnw ./
COPY .mvn .mvn
RUN --mount=type=cache,target=/root/.m2 mvn -B -f pom.xml dependency:go-offline -U

# 复制源码并打包（跳过测试以加速，可在 CI 中单独跑测试）
COPY src ./src
RUN --mount=type=cache,target=/root/.m2 mvn -B -DskipTests package

# Runtime stage
FROM eclipse-temurin:17-jre
LABEL maintainer="you@example.com"
ENV TZ=Asia/Shanghai
WORKDIR /app
# 应用 jar 名称请按实际 target 名称替换
COPY --from=builder /workspace/target/*.jar /app/app.jar
# 建议使用环境变量传入 JVM options / Spring profiles
ENV JAVA_OPTS="-Xms256m -Xmx1g -Dspring.profiles.active=prod"
EXPOSE 8080
HEALTHCHECK --interval=30s --timeout=5s --start-period=30s --retries=3 \
  CMD curl -f http://localhost:8080/actuator/health || exit 1
CMD ["sh", "-lc", "java $JAVA_OPTS -jar /app/app.jar"]
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

## `5002.6.2` `frontend/Dockerfile` + `frontend/nginx.conf`

**1 文件路径：`frontend/Dockerfile`**

```
# frontend/Dockerfile - Vue 多阶段构建 -> nginx
FROM node:18 AS build
WORKDIR /app
COPY package.json yarn.lock ./
RUN yarn install --frozen-lockfile
COPY . .
# 生产构建
RUN yarn build

FROM nginx:stable
LABEL maintainer="you@example.com"
# 可根据需要替换 nginx.conf
COPY nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=build /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

**2 文件路径：`frontend/nginx.conf`**

```
server {
  listen 80;
  server_name _;

  root /usr/share/nginx/html;
  index index.html;

  # 前端路由回退到 index.html
  location / {
    try_files $uri $uri/ /index.html;
  }

  # API 反向代理到后端（k8s 中一般由 Ingress 做此事）
  location /api/ {
    proxy_pass http://REPLACE_BACKEND_HOST:8080/;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }

  # gzip
  gzip on;
  gzip_types text/plain application/json application/javascript text/css text/xml;
}
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

**替换 `REPLACE_BACKEND_HOST` 为后端地址（在 k8s 中可用 Ingress 配置而非 nginx.conf 代理）。**

------

## `5002.6.3 .gitlab-ci.yml`（放在仓库根）

**文件路径：`.gitlab-ci.yml`**

> 说明：该 CI 支持 Docker-in-Docker（使用 `docker:dind`），构建镜像、用 Trivy 扫描、推送到 Harbor，然后用 Helm 部署到 k8s（通过 `KUBE_CONFIG` secret 或 `KUBECONFIG_BASE64`）

```
# .gitlab-ci.yml - GitLab CI for build -> scan -> push -> helm deploy
stages:
  - build
  - scan
  - push
  - deploy

variables:
  DOCKER_DRIVER: overlay2
  HARBOR_REGISTRY: "harbor.example.com"        # <-- 替换为你的 Harbor 地址
  HARBOR_PROJECT: "happyshop"
  IMAGE_TAG: "${CI_COMMIT_SHORT_SHA}"
  HELM_RELEASE: "happyshop"
  HELM_NAMESPACE: "happyshop"

# 缓存 Maven/Node modules 可按需开启
cache:
  key: ${CI_JOB_NAME}
  paths:
    - .m2/repository/

# Backend build -> docker image
build-backend:
  stage: build
  image: docker:24
  services:
    - docker:dind
  variables:
    DOCKER_TLS_CERTDIR: ""
  script:
    - echo "Build backend Jar"
    - cd backend
    - docker build -t $HARBOR_REGISTRY/$HARBOR_PROJECT/jeecg-backend:$IMAGE_TAG -f Dockerfile .
  artifacts:
    expire_in: 1h
  only:
    - branches

# Frontend build
build-frontend:
  stage: build
  image: docker:24
  services:
    - docker:dind
  variables:
    DOCKER_TLS_CERTDIR: ""
  script:
    - echo "Build frontend static and dockerize"
    - cd frontend
    - docker build -t $HARBOR_REGISTRY/$HARBOR_PROJECT/jeecg-frontend:$IMAGE_TAG -f Dockerfile .
  artifacts:
    expire_in: 1h
  only:
    - branches

# Security scan - Trivy (allow failure optionally)
scan:
  stage: scan
  image: aquasec/trivy:latest
  script:
    - echo "Scanning backend image"
    - trivy image --exit-code 1 --severity CRITICAL $HARBOR_REGISTRY/$HARBOR_PROJECT/jeecg-backend:$IMAGE_TAG || true
    - echo "Scanning frontend image"
    - trivy image --exit-code 1 --severity CRITICAL $HARBOR_REGISTRY/$HARBOR_PROJECT/jeecg-frontend:$IMAGE_TAG || true
  allow_failure: true
  dependencies:
    - build-backend
    - build-frontend

# Push images to Harbor
push:
  stage: push
  image: docker:24
  services:
    - docker:dind
  variables:
    DOCKER_TLS_CERTDIR: ""
  before_script:
    - echo $HARBOR_PASSWORD | docker login $HARBOR_REGISTRY -u $HARBOR_USERNAME --password-stdin
  script:
    - docker push $HARBOR_REGISTRY/$HARBOR_PROJECT/jeecg-backend:$IMAGE_TAG
    - docker push $HARBOR_REGISTRY/$HARBOR_PROJECT/jeecg-frontend:$IMAGE_TAG
  only:
    - main
  dependencies:
    - build-backend
    - build-frontend

# Deploy via Helm (requires KUBECONFIG_BASE64 env var saved in GitLab CI/CD variables)
deploy:
  stage: deploy
  image: alpine/helm:3.11.3
  before_script:
    - apk add --no-cache curl bash
    # restore kubeconfig from CI variable
    - echo "$KUBECONFIG_BASE64" | base64 -d > /tmp/kubeconfig
    - export KUBECONFIG=/tmp/kubeconfig
  script:
    - helm repo add stable https://charts.helm.sh/stable || true
    - helm upgrade --install $HELM_RELEASE ops/helm-chart \
        --namespace $HELM_NAMESPACE --create-namespace \
        --set backend.image.repository=$HARBOR_REGISTRY/$HARBOR_PROJECT/jeecg-backend \
        --set backend.image.tag=$IMAGE_TAG \
        --set frontend.image.repository=$HARBOR_REGISTRY/$HARBOR_PROJECT/jeecg-frontend \
        --set frontend.image.tag=$IMAGE_TAG
    - # wait for rollout and run simple sanity checks
    - kubectl --kubeconfig=/tmp/kubeconfig -n $HELM_NAMESPACE rollout status deployment/jeecg-backend --timeout=120s
  only:
    - main
  when: manual
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

> GitLab CI 变量（在仓库 Settings > CI/CD > Variables 中设置）：

```
HARBOR_USERNAME, HARBOR_PASSWORD

KUBECONFIG_BASE64：把 cat ~/.kube/config | base64 -w0 的结果放这里

HARBOR_REGISTRY 可在 YAML 中覆盖
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

------

## `5002.6.4 `Helm chart：`ops/helm-chart`（基础模版）

**1 文件路径：`ops/helm-chart/Chart.yaml`**

```
apiVersion: v2
name: happyshop
description: Helm chart for Happyshop (frontend + backend)
version: 0.1.0
appVersion: "1.0.0"
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

**2 文件路径：`ops/helm-chart/values.yaml`**

```
replicaCount: 2

imagePullSecrets: []
# backend config
backend:
  image:
    repository: "harbor.example.com/happyshop/jeecg-backend"
    tag: "latest"
  port: 8080
  resources:
    requests:
      cpu: 200m
      memory: 512Mi
    limits:
      cpu: 1
      memory: 1Gi

# frontend config
frontend:
  image:
    repository: "harbor.example.com/happyshop/jeecg-frontend"
    tag: "latest"
  port: 80
  resources:
    requests:
      cpu: 100m
      memory: 128Mi
    limits:
      cpu: 500m
      memory: 256Mi

ingress:
  enabled: true
  host: "your.domain.com"   # <-- 替换
  annotations: {}
  tls: false

serviceAccount:
  create: true
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

**3 文件路径：`ops/helm-chart/templates/backend-deployment.yaml`**

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "happyshop.fullname" . }}-backend
  labels:
    app: {{ include "happyshop.name" . }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: {{ include "happyshop.name" . }}
      tier: backend
  template:
    metadata:
      labels:
        app: {{ include "happyshop.name" . }}
        tier: backend
    spec:
      imagePullSecrets:
        {{- toYaml .Values.imagePullSecrets | nindent 8 }}
      containers:
        - name: jeecg-backend
          image: "{{ .Values.backend.image.repository }}:{{ .Values.backend.image.tag }}"
          ports:
            - containerPort: {{ .Values.backend.port }}
          readinessProbe:
            httpGet:
              path: /actuator/health
              port: {{ .Values.backend.port }}
            initialDelaySeconds: 10
            periodSeconds: 10
          livenessProbe:
            httpGet:
              path: /actuator/health
              port: {{ .Values.backend.port }}
            initialDelaySeconds: 30
            periodSeconds: 30
          resources:
{{ toYaml .Values.backend.resources | indent 12 }}
---
apiVersion: v1
kind: Service
metadata:
  name: {{ include "happyshop.fullname" . }}-backend
spec:
  selector:
    app: {{ include "happyshop.name" . }}
    tier: backend
  ports:
    - port: {{ .Values.backend.port }}
      targetPort: {{ .Values.backend.port }}
      protocol: TCP
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

**4 文件路径：`ops/helm-chart/templates/frontend-deployment.yaml`**

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "happyshop.fullname" . }}-frontend
  labels:
    app: {{ include "happyshop.name" . }}
spec:
  replicas: 1
  selector:
    matchLabels:
      app: {{ include "happyshop.name" . }}
      tier: frontend
  template:
    metadata:
      labels:
        app: {{ include "happyshop.name" . }}
        tier: frontend
    spec:
      imagePullSecrets:
        {{- toYaml .Values.imagePullSecrets | nindent 8 }}
      containers:
        - name: jeecg-frontend
          image: "{{ .Values.frontend.image.repository }}:{{ .Values.frontend.image.tag }}"
          ports:
            - containerPort: {{ .Values.frontend.port }}
          resources:
{{ toYaml .Values.frontend.resources | indent 12 }}
---
apiVersion: v1
kind: Service
metadata:
  name: {{ include "happyshop.fullname" . }}-frontend
spec:
  selector:
    app: {{ include "happyshop.name" . }}
    tier: frontend
  ports:
    - port: {{ .Values.frontend.port }}
      targetPort: {{ .Values.frontend.port }}
      protocol: TCP
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

**5 文件路径：`ops/helm-chart/templates/ingress.yaml`**

```
{{- if .Values.ingress.enabled }}
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: {{ include "happyshop.fullname" . }}-ingress
  annotations:
    {{- toYaml .Values.ingress.annotations | nindent 4 }}
spec:
  rules:
    - host: {{ .Values.ingress.host }}
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: {{ include "happyshop.fullname" . }}-frontend
                port:
                  number: {{ .Values.frontend.port }}
          - path: /api
            pathType: Prefix
            backend:
              service:
                name: {{ include "happyshop.fullname" . }}-backend
                port:
                  number: {{ .Values.backend.port }}
{{- end }}
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

**6 文件路径：`ops/helm-chart/templates/_helpers.tpl`**

```
{{- define "happyshop.name" -}}
happyshop
{{- end -}}

{{- define "happyshop.fullname" -}}
{{ include "happyshop.name" . }}
{{- end -}}
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

**以上 Helm chart 为极简版本，可按需把 ConfigMap、Secrets、HorizontalPodAutoscaler、NetworkPolicy、ServiceAccount、RBAC 等添加进去。**

------

## `5002.6.5 `MySQL StatefulSet + PVC + Backup CronJob

**1 文件路径：`ops/k8s-manifests/mysql-statefulset.yaml`**

```
apiVersion: v1
kind: Namespace
metadata:
  name: happyshop
---
apiVersion: v1
kind: Secret
metadata:
  name: mysql-secret
  namespace: happyshop
type: Opaque
stringData:
  mysql-root-password: "REPLACE_STRONG_PASSWORD"   # <-- 替换或从 sealedsecret/vault 注入
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
  namespace: happyshop
spec:
  serviceName: mysql
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:8.0
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: mysql-root-password
        ports:
        - containerPort: 3306
          name: mysql
        volumeMounts:
        - name: mysql-data
          mountPath: /var/lib/mysql
        resources:
          requests:
            memory: "512Mi"
            cpu: "200m"
          limits:
            memory: "1Gi"
            cpu: "1"
  volumeClaimTemplates:
  - metadata:
      name: mysql-data
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 20Gi
---
apiVersion: v1
kind: Service
metadata:
  name: mysql
  namespace: happyshop
spec:
  ports:
  - port: 3306
    name: mysql
  clusterIP: None
  selector:
    app: mysql
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

**2 文件路径：`ops/k8s-manifests/mysql-backup-cronjob.yaml`**

```
apiVersion: batch/v1
kind: CronJob
metadata:
  name: mysql-backup
  namespace: happyshop
spec:
  schedule: "0 3 * * *"   # 每日 03:00 运行
  successfulJobsHistoryLimit: 3
  failedJobsHistoryLimit: 1
  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: backup
              image: bitnami/mysql:8.0
              env:
                - name: MYSQL_HOST
                  value: mysql
                - name: MYSQL_ROOT_PASSWORD
                  valueFrom:
                    secretKeyRef:
                      name: mysql-secret
                      key: mysql-root-password
              command:
                - /bin/sh
                - -c
                - |
                  mkdir -p /backup
                  ts=$(date +%F_%H%M%S)
                  mysqldump -h $MYSQL_HOST -u root -p$MYSQL_ROOT_PASSWORD --all-databases --single-transaction --quick --lock-tables=false > /backup/mysql-${ts}.sql
                  # 可增加上传 OSS / S3 的命令，例如使用 aws cli / ossutil
                  # ossutil cp /backup/mysql-${ts}.sql oss://your-bucket/backup/
          restartPolicy: OnFailure
          volumes:
            - name: backup-pvc
              persistentVolumeClaim:
                claimName: mysql-backup-pvc
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-backup-pvc
  namespace: happyshop
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 20Gi
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

**说明：上面 CronJob 把备份写到 PVC 中。生产建议直接把备份上传到对象存储（阿里 OSS / S3），在容器内安装 `ossutil` 或 `awscli` 并把凭证放在 k8s secret 中。**

------

## 5002.6.6 Loki + Promtail + Grafana 最小清单（快速查看日志）

**文件路径：`ops/k8s-manifests/loki-promtail-grafana.yaml`**

> 这是极简合并清单，用于快速搭起 Loki+Promtail+Grafana。为方便起见使用官方 Helm charts 更安全、灵活；下面只是最小化示例，可按需用 Helm 替换。

```
# namespace
apiVersion: v1
kind: Namespace
metadata:
  name: monitoring
---
# Grafana (简化 deployment)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: grafana
  namespace: monitoring
spec:
  replicas: 1
  selector:
    matchLabels:
      app: grafana
  template:
    metadata:
      labels:
        app: grafana
    spec:
      containers:
        - name: grafana
          image: grafana/grafana:9.0.0
          ports:
            - containerPort: 3000
---
apiVersion: v1
kind: Service
metadata:
  name: grafana
  namespace: monitoring
spec:
  selector:
    app: grafana
  ports:
    - port: 3000
      targetPort: 3000
---
# Loki single-binary (简化)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: loki
  namespace: monitoring
spec:
  replicas: 1
  selector:
    matchLabels:
      app: loki
  template:
    metadata:
      labels:
        app: loki
    spec:
      containers:
      - name: loki
        image: grafana/loki:2.6.1
        args:
          - -config.file=/etc/loki/local-config.yaml
        volumeMounts:
          - name: loki-config
            mountPath: /etc/loki
      volumes:
        - name: loki-config
          configMap:
            name: loki-config
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: loki-config
  namespace: monitoring
data:
  local-config.yaml: |
    auth_enabled: false
    server:
      http_listen_port: 3100
    ingester:
      lifecycler:
        address: 127.0.0.1
        ring:
          kvstore:
            store: inmemory
      chunk_idle_period: 5m
      max_transfer_retries: 0
    schema_config:
      configs:
        - from: 2020-10-15
          store: boltdb-shipper
          object_store: filesystem
          schema: v11
          index:
            prefix: index_
            period: 168h
    storage_config:
      boltdb_shipper:
        active_index_directory: /data/loki/index
        cache_location: /data/loki/cache
        shared_store: filesystem
      filesystem:
        directory: /data/loki/chunks
    limits_config:
      enforce_metric_name: false
---
# Promtail (daemonset simplified)
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: promtail
  namespace: monitoring
spec:
  selector:
    matchLabels:
      app: promtail
  template:
    metadata:
      labels:
        app: promtail
    spec:
      containers:
        - name: promtail
          image: grafana/promtail:2.6.1
          args:
            - -config.file=/etc/promtail/promtail.yaml
          volumeMounts:
            - name: promtail-config
              mountPath: /etc/promtail
            - name: varlog
              mountPath: /var/log
            - name: varlibdockercontainers
              mountPath: /var/lib/docker/containers
      volumes:
        - name: promtail-config
          configMap:
            name: promtail-config
        - name: varlog
          hostPath:
            path: /var/log
        - name: varlibdockercontainers
          hostPath:
            path: /var/lib/docker/containers
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: promtail-config
  namespace: monitoring
data:
  promtail.yaml: |
    server:
      http_listen_port: 9080
    positions:
      filename: /tmp/positions.yaml
    clients:
      - url: http://loki:3100/loki/api/v1/push
    scrape_configs:
      - job_name: kubernetes
        static_configs:
          - targets:
              - localhost
            labels:
              job: varlogs
              __path__: /var/log/*log
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

> 提示：生产建议使用官方 Helm charts：

- `grafana` chart（提供 datasource 自动创建）
- `loki` chart
- `promtail` chart
   使用 Helm 可以更容易地配置持久存储、认证、PSA。

------

## 5002.6.7 辅助脚本与常用命令片段

**`1 scripts/helm-deploy.sh`（可在本地或 CI 中使用）**

```
#!/bin/bash
set -e
NAMESPACE=happyshop
RELEASE=happyshop
HELM_CHART_PATH=ops/helm-chart
IMAGE_TAG=$1
if [ -z "$IMAGE_TAG" ]; then
  echo "Usage: $0 <image-tag>"
  exit 1
fi
helm upgrade --install $RELEASE $HELM_CHART_PATH \
  --namespace $NAMESPACE --create-namespace \
  --set backend.image.tag=$IMAGE_TAG \
  --set frontend.image.tag=$IMAGE_TAG
kubectl -n $NAMESPACE rollout status deployment/jeecg-backend --timeout=120s
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

**2 常用 kubectl 命令（替代 `tail -f /.../app.log`）：**

```
# 查看某个 deployment 的 pod 并 tail 日志
kubectl -n happyshop get pods -l app=happyshop,tier=backend
kubectl -n happyshop logs -f <pod-name> -c jeecg-backend

# 多 pod tail 推荐 stern（本机安装 stern）
stern jeecg-backend -n happyshop

# 查看 pod 资源使用
kubectl top pod -n happyshop
kubectl top node
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

------

## 5002.6.8 替换 / 配置提示（务必阅读）

- 在 `.gitlab-ci.yml` 中替换 `harbor.example.com`、`KUBECONFIG_BASE64`、`HARBOR_USERNAME`、`HARBOR_PASSWORD`。CI 中 `deploy` job 使用 `when: manual` 可防误触，若想自动部署去掉 `when: manual`。
- `values.yaml` 中把 `ingress.host` 替为真实域名并配置域名解析与 ingress controller（nginx-ingress 或 cloud-managed）。
- secrets（DB 密码、OSS/阿里云凭证等）**必须**通过 k8s Secret、SealedSecrets 或 Vault 管理，切勿硬编码到文件或镜像中。
- 如果你使用阿里云 ACK，建议使用阿里云 ACR 替代 Harbor 或配置 Harbor 与 ACR 镜像同步。
- 生产建议至少两个节点或使用 ACK 等托管 k8s 来避免单点故障。单机 k3s 仅用于测试/轻量生产。

------

## 5002.6.9 下一步建议（优先级）

1. 把仓库迁到 GitLab，添加 `.gitlab-ci.yml` 并注册 runner（docker executor）。
2. 在本地或 CI 里测试构建并推到 Harbor（先手动登录并推一次）。
3. 在一台测试机器上安装 k3s（或在现有服务器上新建 VM 安装 k3s），并将 `KUBECONFIG_BASE64` 放入 GitLab 变量，测试 Helm 部署。
4. 部署 Prometheus/Grafana（或 kube-prometheus-stack），并把后端的 micrometer/prometheus endpoint 打开。
5. 部署 Loki + promtail 或 EFK 来集中日志，停止把日志写到本地文件（或同步写 stdout）——Spring Boot 推荐 `logback` 输出到 stdout。
6. 将数据库迁移到 RDS（或至少单独 VM），并用 CronJob 做备份到 OSS。

------

# 5002.7 针对5002.6中文件、配置的完整细化输出

如果你需要我现在做下面其中一项的**完整细化输出**（我会把文件内容贴出来并写明如何在服务器上一步步运行）——我会直接输出，不再问你：

- A. 把 Helm Chart 扩展为带 ConfigMap、Secret、HPA、ingress TLS 的“production-ready”版本（并给出 values-prod.yaml）
- B. 把 `.gitlab-ci.yml` 改成 Jenkinsfile（如果你坚持使用 Jenkins）
- C. 把 Loki/Promtail/Grafana 替换为 Helm 部署脚本 + Grafana Dashboards JSON
- D. 把 MySQL 备份脚本改为上传阿里云 OSS 的完整脚本（含 `ossutil` 用法和 k8s secret）

说你要哪一个（比如回复“A”）或者直接告诉我你要哪部分先更深入，我就直接在下一条消息里把那部分完整展开（包括所有命令与示例）。

------

好。下面一次性把 **A → D** 的完整可直接复制/粘贴的文件、Helm 模板、Jenkinsfile、Helm/helmfile/helm命令、Grafana dashboard JSON、以及把 MySQL 备份上传到阿里 OSS 的完整 CronJob 脚本和 k8s Secret 示例一次给你。你只需把 `REPLACE_...` 的字段改成你自己的值，然后按我写的命令执行即可。

我把内容分为四块（A、B、C、D），每块包含文件路径说明、文件内容、以及关键的部署/安装/测试命令。为了便于你复制，文件内容都以文件名标注。避免重复提问 — 我直接给出可用的 production-ready 模板与操作步骤。

------

## 5002.7.1 A. Helm Chart — production-ready（包含 ConfigMap、Secret、HPA、Ingress TLS、PVC、probes、autoscaling）

**位置建议：`ops/helm-chart/`（替换你已有的 chart）**

**必要文件清单（我在下面给出每个文件内容）**

- `Chart.yaml`
- `values-prod.yaml`
- `templates/backend-deployment.yaml`
- `templates/frontend-deployment.yaml`
- `templates/secrets.yaml`
- `templates/configmap.yaml`
- `templates/hpa.yaml`
- `templates/pvc.yaml`
- `templates/ingress-tls.yaml`
- `templates/service-backend.yaml`
- `templates/service-frontend.yaml`
- `templates/_helpers.tpl`

------

### 1 ops/helm-chart/Chart.yaml

```
apiVersion: v2
name: happyshop
description: Production Helm chart for Happyshop
type: application
version: 1.0.0
appVersion: "1.0.0"
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

### 2 ops/helm-chart/values-prod.yaml

```
replicaCount: 3

imagePullSecrets:
  - name: regcred   # 创建 docker-registry secret 名称

backend:
  image:
    repository: "harbor.example.com/happyshop/jeecg-backend"
    tag: "REPLACE_TAG"
    pullPolicy: IfNotPresent
  port: 8080
  resources:
    requests:
      cpu: 300m
      memory: 512Mi
    limits:
      cpu: 1500m
      memory: 2Gi
  env:
    SPRING_PROFILES_ACTIVE: prod
  probes:
    readinessPath: /actuator/health
    livenessPath: /actuator/health
    initialDelaySeconds: 10
    periodSeconds: 10

frontend:
  image:
    repository: "harbor.example.com/happyshop/jeecg-frontend"
    tag: "REPLACE_TAG"
    pullPolicy: IfNotPresent
  port: 80
  resources:
    requests:
      cpu: 150m
      memory: 128Mi
    limits:
      cpu: 500m
      memory: 512Mi

ingress:
  enabled: true
  className: nginx
  host: "app.example.com"
  tls:
    enabled: true
    secretName: "happyshop-tls" # TLS secret name in namespace (created by cert-manager or manually)
  annotations:
    kubernetes.io/ingress.class: "nginx"
    cert-manager.io/cluster-issuer: "letsencrypt-prod" # 如果用 cert-manager

secrets:
  mysql:
    rootPasswordSecretName: "mysql-root-password" # k8s secret 名
  jwt:
    secretName: "jwt-secret"

persistence:
  enabled: true
  storageClass: "default"
  size: 20Gi

autoscaling:
  enabled: true
  minReplicas: 2
  maxReplicas: 6
  targetCPUUtilizationPercentage: 60

monitoring:
  prometheus:
    scrapePath: /actuator/prometheus
    enabled: true
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

### 3 ops/helm-chart/templates/_helpers.tpl

```
{{- define "happyshop.name" -}}
happyshop
{{- end -}}

{{- define "happyshop.fullname" -}}
{{ include "happyshop.name" . }}
{{- end -}}
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

### 4 ops/helm-chart/templates/secrets.yaml

```
{{- if .Values.secrets }}
---
apiVersion: v1
kind: Secret
metadata:
  name: {{ .Values.secrets.mysql.rootPasswordSecretName }}
type: Opaque
stringData:
  mysql-root-password: {{ .Files.Get "secrets/mysql-root-password.txt" | default "REPLACE_MYSQL_ROOT_PASSWORD" }}
---
apiVersion: v1
kind: Secret
metadata:
  name: {{ .Values.secrets.jwt.secretName }}
type: Opaque
stringData:
  jwt-secret: {{ .Files.Get "secrets/jwt-secret.txt" | default "REPLACE_JWT_SECRET" }}
{{- end }}
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

### 说明：可以把敏感值放在 chart 的 `files` 目录（`ops/helm-chart/secrets/...`），但生产更推荐使用 SealedSecrets / Vault 并直接在 k8s 集群创建 secret，而不要把密码放到 chart。`default` 仅为示例。

### 5 ops/helm-chart/templates/configmap.yaml

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ include "happyshop.fullname" . }}-config
data:
  application.properties: |
    # Add config overrides for production environment if needed
    spring.datasource.url=jdbc:mysql://mysql.happyshop.svc.cluster.local:3306/jeecg?useSSL=false&serverTimezone=UTC
    spring.datasource.username=root
    spring.datasource.password=${MYSQL_ROOT_PASSWORD}
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

### 如果用 Secret 注入敏感值，则在 deployment 中使用 envFrom secretRef 或 env valueFrom。

### 6 ops/helm-chart/templates/service-backend.yaml

```
apiVersion: v1
kind: Service
metadata:
  name: {{ include "happyshop.fullname" . }}-backend
spec:
  selector:
    app: {{ include "happyshop.name" . }}
    tier: backend
  ports:
    - name: http
      port: {{ .Values.backend.port }}
      targetPort: {{ .Values.backend.port }}
  type: ClusterIP
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

### 7 ops/helm-chart/templates/service-frontend.yaml

```
apiVersion: v1
kind: Service
metadata:
  name: {{ include "happyshop.fullname" . }}-frontend
spec:
  selector:
    app: {{ include "happyshop.name" . }}
    tier: frontend
  ports:
    - name: http
      port: {{ .Values.frontend.port }}
      targetPort: {{ .Values.frontend.port }}
  type: ClusterIP
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

### 8 ops/helm-chart/templates/backend-deployment.yaml

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "happyshop.fullname" . }}-backend
  labels:
    app: {{ include "happyshop.name" . }}
    tier: backend
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: {{ include "happyshop.name" . }}
      tier: backend
  template:
    metadata:
      labels:
        app: {{ include "happyshop.name" . }}
        tier: backend
    spec:
      imagePullSecrets:
        {{- toYaml .Values.imagePullSecrets | nindent 8 }}
      containers:
        - name: jeecg-backend
          image: "{{ .Values.backend.image.repository }}:{{ .Values.backend.image.tag }}"
          imagePullPolicy: {{ .Values.backend.image.pullPolicy }}
          ports:
            - containerPort: {{ .Values.backend.port }}
          env:
            - name: SPRING_PROFILES_ACTIVE
              value: "{{ .Values.backend.env.SPRING_PROFILES_ACTIVE | default "prod" }}"
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: {{ .Values.secrets.mysql.rootPasswordSecretName }}
                  key: mysql-root-password
          livenessProbe:
            httpGet:
              path: {{ .Values.backend.probes.livenessPath }}
              port: {{ .Values.backend.port }}
            initialDelaySeconds: {{ .Values.backend.probes.initialDelaySeconds }}
            periodSeconds: {{ .Values.backend.probes.periodSeconds }}
          readinessProbe:
            httpGet:
              path: {{ .Values.backend.probes.readinessPath }}
              port: {{ .Values.backend.port }}
            initialDelaySeconds: {{ .Values.backend.probes.initialDelaySeconds }}
            periodSeconds: {{ .Values.backend.probes.periodSeconds }}
          resources:
{{ toYaml .Values.backend.resources | indent 12 }}
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

### 9 ops/helm-chart/templates/frontend-deployment.yaml

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "happyshop.fullname" . }}-frontend
  labels:
    app: {{ include "happyshop.name" . }}
    tier: frontend
spec:
  replicas: 1
  selector:
    matchLabels:
      app: {{ include "happyshop.name" . }}
      tier: frontend
  template:
    metadata:
      labels:
        app: {{ include "happyshop.name" . }}
        tier: frontend
    spec:
      imagePullSecrets:
        {{- toYaml .Values.imagePullSecrets | nindent 8 }}
      containers:
        - name: jeecg-frontend
          image: "{{ .Values.frontend.image.repository }}:{{ .Values.frontend.image.tag }}"
          imagePullPolicy: {{ .Values.frontend.image.pullPolicy }}
          ports:
            - containerPort: {{ .Values.frontend.port }}
          resources:
{{ toYaml .Values.frontend.resources | indent 12 }}
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

### 10 ops/helm-chart/templates/hpa.yaml

```
{{- if .Values.autoscaling.enabled }}
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: {{ include "happyshop.fullname" . }}-backend-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: {{ include "happyshop.fullname" . }}-backend
  minReplicas: {{ .Values.autoscaling.minReplicas }}
  maxReplicas: {{ .Values.autoscaling.maxReplicas }}
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: {{ .Values.autoscaling.targetCPUUtilizationPercentage }}
{{- end }}
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

### 11 ops/helm-chart/templates/pvc.yaml

```
{{- if .Values.persistence.enabled }}
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: {{ include "happyshop.fullname" . }}-data
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: {{ .Values.persistence.storageClass | quote }}
  resources:
    requests:
      storage: {{ .Values.persistence.size }}
{{- end }}
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

### 12 ops/helm-chart/templates/ingress-tls.yaml

```
{{- if .Values.ingress.enabled }}
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: {{ include "happyshop.fullname" . }}-ingress
  annotations:
    {{- toYaml .Values.ingress.annotations | nindent 4 }}
spec:
  ingressClassName: {{ .Values.ingress.className }}
  tls:
    - hosts:
        - {{ .Values.ingress.host }}
      secretName: {{ .Values.ingress.tls.secretName }}
  rules:
    - host: {{ .Values.ingress.host }}
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: {{ include "happyshop.fullname" . }}-frontend
                port:
                  number: {{ .Values.frontend.port }}
          - path: /api
            pathType: Prefix
            backend:
              service:
                name: {{ include "happyshop.fullname" . }}-backend
                port:
                  number: {{ .Values.backend.port }}
{{- end }}
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

------

### 13 A 部署 / 测试步骤（简明）

1）在 git 仓库 `ops/helm-chart` 下放上面文件，并在 `values-prod.yaml` 填入真实值（domain、harbor repo、tags）。

2）在集群中创建 `regcred`（docker registry secret）:

```
kubectl create secret docker-registry regcred \
  --docker-server=harbor.example.com \
  --docker-username=HARBOR_USER \
  --docker-password='HARBOR_PASSWORD' \
  --docker-email=you@example.com -n happyshop
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

3）使用 SealedSecrets 或直接创建 mysql/jwt secret（推荐 SealedSecrets）：

```
kubectl create secret generic mysql-root-password \
  --from-literal=mysql-root-password='YOUR_PASSWORD' -n happyshop
kubectl create secret generic jwt-secret --from-literal=jwt-secret='RANDOM_JWT_SECRET' -n happyshop
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

4）安装 chart（本地 Helm）：

```
helm upgrade --install happyshop ./ops/helm-chart -n happyshop --values ./ops/helm-chart/values-prod.yaml
kubectl -n happyshop rollout status deployment/happyshop-backend --timeout=180s
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

------

## 5002.7.2 B. Jenkinsfile（若你坚持用 Jenkins 而不是 GitLab CI）

把这个 Jenkinsfile 放到项目根（或 `backend/`、`frontend/` 对应仓库）。假设你的 Jenkins 已安装 Docker + Docker-in-Docker 或有 Docker agents，并且已配置凭据（Harbor credentials id：`harbor-creds`，kubeconfig 在凭据 `kubeconfig`）。

**`Jenkinsfile`（声明式 pipeline）**

```
pipeline {
  agent { label 'docker' }   // 或指定有 docker 的 node
  environment {
    HARBOR_REGISTRY = 'harbor.example.com'
    HARBOR_PROJECT = 'happyshop'
    IMAGE_TAG = "${env.BUILD_ID}-${env.GIT_COMMIT.take(7)}"
  }
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Build backend image') {
      steps {
        dir('backend') {
          script {
            sh "docker build -t ${HARBOR_REGISTRY}/${HARBOR_PROJECT}/jeecg-backend:${IMAGE_TAG} -f Dockerfile ."
          }
        }
      }
    }
    stage('Build frontend image') {
      steps {
        dir('frontend') {
          sh "docker build -t ${HARBOR_REGISTRY}/${HARBOR_PROJECT}/jeecg-frontend:${IMAGE_TAG} -f Dockerfile ."
        }
      }
    }
    stage('Scan images') {
      steps {
        script {
          // 假设 trivy 在 agent 上可用
          sh "trivy image --exit-code 1 --severity CRITICAL ${HARBOR_REGISTRY}/${HARBOR_PROJECT}/jeecg-backend:${IMAGE_TAG} || true"
          sh "trivy image --exit-code 1 --severity CRITICAL ${HARBOR_REGISTRY}/${HARBOR_PROJECT}/jeecg-frontend:${IMAGE_TAG} || true"
        }
      }
    }
    stage('Push to Harbor') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'harbor-creds', usernameVariable: 'H_USER', passwordVariable: 'H_PASS')]) {
          sh "docker login ${HARBOR_REGISTRY} -u ${H_USER} -p ${H_PASS}"
          sh "docker push ${HARBOR_REGISTRY}/${HARBOR_PROJECT}/jeecg-backend:${IMAGE_TAG}"
          sh "docker push ${HARBOR_REGISTRY}/${HARBOR_PROJECT}/jeecg-frontend:${IMAGE_TAG}"
        }
      }
    }
    stage('Deploy to K8s') {
      steps {
        withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG_FILE')]) {
          sh 'export KUBECONFIG=$KUBECONFIG_FILE'
          sh "helm upgrade --install happyshop ops/helm-chart --namespace happyshop --create-namespace --set backend.image.tag=${IMAGE_TAG} --set frontend.image.tag=${IMAGE_TAG}"
          sh "kubectl -n happyshop rollout status deployment/happyshop-backend --timeout=180s"
        }
      }
    }
  }
  post {
    failure {
      mail to: 'devops@example.com', subject: "Build failed: ${env.JOB_NAME}", body: "Build ${env.BUILD_NUMBER} failed"
    }
  }
}
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

> Jenkins 要点：

- 在 Jenkins 管理凭据中添加 Harbor 用户/密码（id=`harbor-creds`），添加 kubeconfig 文件凭据（id=`kubeconfig`）。
- 构建 agent 需支持 docker（或者使用 Kubernetes plugin 与 docker-in-docker）。
- 若使用 Jenkins + k8s agent，考虑使用 Kubernetes plugin 动态生成 agent。

------

## 5002.7.3 C. Loki/Promtail/Grafana 使用 Helm 部署 + Grafana Dashboard JSON（生产推荐用 Helm）

下面给出 **Helm 安装命令**（官方 charts）和一个 **Grafana dashboard JSON**（展示：后端 Pod CPU/Mem, response time, errors, logs via Loki datasource）。

> 假设你已经安装 Helm 3，并且已添加 Grafana 和 Loki chart 仓库。

### 1 Helm 安装（简要命令）

```
# 添加 chart repos
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update

# 安装 Loki + Promtail
helm upgrade --install loki grafana/loki-stack -n monitoring --create-namespace \
  --set grafana.enabled=false \
  --set promtail.enabled=true \
  --set loki.persistence.enabled=true \
  --set loki.persistence.size=20Gi

# 安装 Grafana
helm upgrade --install grafana grafana/grafana -n monitoring \
  --set persistence.enabled=true \
  --set persistence.size=10Gi \
  --set adminUser=admin \
  --set adminPassword='REPLACE_ADMIN_PWD' \
  --set ingress.enabled=false
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

> 若在 k8s cloud env 推荐开启 ingress、TLS 与 grafana admin 密码从 secret 注入。

安装后获取 grafana admin 密码：

```
kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
kubectl port-forward svc/grafana 3000:80 -n monitoring
# 然后在 http://localhost:3000 登录
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

### 2 Grafana Dashboard JSON（示例简易版 — 保存为 `grafana/dashboard-happyshop.json`）

> 这个 Dashboard 包含：Pod CPU/Mem（Prometheus），后端 `/actuator/metrics` 指标（response time if exposed），以及 Loki 日志面板（通过 Log labels 搜索 `app="happyshop", tier="backend"`）。

```
{
  "annotations": {"list": []},
  "panels": [
    {
      "type": "graph",
      "title": "Backend Pod CPU Usage",
      "targets": [
        {
          "expr": "sum(rate(container_cpu_usage_seconds_total{namespace=\"happyshop\",pod=~\"happyshop-backend-.*\"}[5m])) by (pod)",
          "format": "time_series"
        }
      ],
      "gridPos": {"h": 8, "w": 12, "x": 0, "y": 0}
    },
    {
      "type": "graph",
      "title": "Backend Pod Memory Usage",
      "targets": [
        {
          "expr": "sum(container_memory_usage_bytes{namespace=\"happyshop\",pod=~\"happyshop-backend-.*\"}) by (pod)",
          "format": "time_series"
        }
      ],
      "gridPos": {"h": 8, "w": 12, "x": 12, "y": 0}
    },
    {
      "type": "logs",
      "title": "Backend Logs (Loki)",
      "datasource": { "type": "loki", "uid": "loki" },
      "targets": [
        {
          "expr": "{namespace=\"happyshop\",app=\"happyshop\",tier=\"backend\"}"
        }
      ],
      "gridPos": {"h": 8, "w": 24, "x": 0, "y": 8}
    }
  ],
  "title": "Happyshop Overview",
  "schemaVersion": 30,
  "version": 1
}
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

在 Grafana 中添加 Loki datasource（URL 指向 `http://loki:3100` 或对应 Service），然后通过 UI 导入上面的 dashboard JSON。

------

## 5002.7.4 D. MySQL 备份并上传到阿里云 OSS（完整脚本 + k8s CronJob 示例 + Secret 配置）

**目标：每天自动做 mysqldump、压缩，并上传到阿里云 OSS。**使用 `ossutil` 是最直接的方法。下面给出两种选项：

- 选项 1：在 CronJob 容器内安装并用 `ossutil` 上传（我给出用 `registry.cn-hangzhou.aliyuncs.com/acs/ossutil:latest` 这种镜像为示例，如果你没有此镜像可构建一个包含 `ossutil` 的镜像）。
- 选项 2：使用 `python` 的 `oss2` 在轻量镜像中上传（提供 Python 脚本）。

我给出 **CronJob（ossutil）** 的完整 production-ready 示例，并配套 k8s Secret 用于保存 AccessKey。

------

### 1 在 k8s 创建 OSS 凭据 Secret

```
kubectl -n happyshop create secret generic aliyun-oss-cred \
  --from-literal=OSS_ACCESS_KEY_ID='REPLACE_AK' \
  --from-literal=OSS_ACCESS_KEY_SECRET='REPLACE_SK' \
  --from-literal=OSS_BUCKET='your-bucket-name' \
  --from-literal=OSS_ENDPOINT='oss-cn-hangzhou.aliyuncs.com'
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

------

### 2 CronJob YAML（使用包含 ossutil 的镜像）

> 如果你没有合适镜像，可以先自己构建一个基于 `alpine` 安装 `curl`，把 `ossutil` 放进去，并 push 到你的 Harbor，然后把镜像名称替换到 `image:` 字段。下面示例假设你已有镜像 `myrepo/oss-backup:latest`，该镜像包含 `mysqldump`（或 `mysql-client`）与 `ossutil`。

**文件路径：`ops/k8s-manifests/mysql-backup-oss-cronjob.yaml`**

```
apiVersion: batch/v1
kind: CronJob
metadata:
  name: mysql-backup-oss
  namespace: happyshop
spec:
  schedule: "0 3 * * *"  # 每天 03:00
  successfulJobsHistoryLimit: 7
  failedJobsHistoryLimit: 1
  jobTemplate:
    spec:
      template:
        spec:
          restartPolicy: OnFailure
          containers:
            - name: mysql-backup
              image: your-registry/oss-backup:latest   # <-- 替换：包含 mysqldump + ossutil
              env:
                - name: MYSQL_HOST
                  value: mysql
                - name: MYSQL_USER
                  value: root
                - name: MYSQL_ROOT_PASSWORD
                  valueFrom:
                    secretKeyRef:
                      name: mysql-root-password
                      key: mysql-root-password
                - name: OSS_ACCESS_KEY_ID
                  valueFrom:
                    secretKeyRef:
                      name: aliyun-oss-cred
                      key: OSS_ACCESS_KEY_ID
                - name: OSS_ACCESS_KEY_SECRET
                  valueFrom:
                    secretKeyRef:
                      name: aliyun-oss-cred
                      key: OSS_ACCESS_KEY_SECRET
                - name: OSS_BUCKET
                  valueFrom:
                    secretKeyRef:
                      name: aliyun-oss-cred
                      key: OSS_BUCKET
                - name: OSS_ENDPOINT
                  valueFrom:
                    secretKeyRef:
                      name: aliyun-oss-cred
                      key: OSS_ENDPOINT
              command:
                - /bin/sh
                - -c
                - |
                  set -e
                  ts=$(date +%F_%H%M%S)
                  dumpfile=/tmp/mysql-${ts}.sql
                  echo "Dumping DB to ${dumpfile}"
                  mysqldump -h $MYSQL_HOST -u $MYSQL_USER -p"$MYSQL_ROOT_PASSWORD" --all-databases --single-transaction --quick --lock-tables=false > ${dumpfile}
                  gzip ${dumpfile}
                  upload_file="${dumpfile}.gz"
                  echo "Config ossutil"
                  /usr/local/bin/ossutil64 config --access-key-id=$OSS_ACCESS_KEY_ID --access-key-secret=$OSS_ACCESS_KEY_SECRET --endpoint=$OSS_ENDPOINT --output=json
                  echo "Uploading to OSS: ${OSS_BUCKET}/mysql-backups/$(basename ${upload_file})"
                  /usr/local/bin/ossutil64 cp ${upload_file} oss://$OSS_BUCKET/mysql-backups/ --timeout=3600
                  echo "Upload complete"
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

> 说明：

- `ossutil64` 是阿里提供的 OSS 客户端二进制（通常名为 `ossutil64` 或 `ossutil`）。镜像需包含该二进制并位于 `/usr/local/bin/ossutil64`。
- 若你无法构建镜像，另一种做法是使用 `aliyun-cli`（pip install aliyun-python-sdk-oss）或 `python` + `oss2`，我也可给出 Python 版本脚本（如需我把该镜像 Dockerfile /构建步骤也给出，告诉我，我会把 Dockerfile 一并输出）。

------

### 3 如何自建 `oss-backup` 镜像（Dockerfile 示例）

**文件：`ops/docker/oss-backup/Dockerfile`**

```
FROM alpine:3.18
RUN apk add --no-cache curl bash gzip mysql-client
# 下载 ossutil（示例链接可能随时间变更，建议手动把 ossutil 放到构建上下文）
# 这里假设你事先把 ossutil64 放到同目录
COPY ossutil64 /usr/local/bin/ossutil64
RUN chmod +x /usr/local/bin/ossutil64
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

`entrypoint.sh` 内容与上面 CronJob command 内脚本相同（把逻辑放在脚本内更清晰）。构建并 push：

```
docker build -t harbor.example.com/happyshop/oss-backup:latest -f ops/docker/oss-backup/Dockerfile ops/docker/oss-backup
docker push harbor.example.com/happyshop/oss-backup:latest
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

------

## 5002.7.5 恢复演练（验证备份）

1. 在对象存储查看 `mysql-backups` 是否有 `.sql.gz` 文件。
2. 拉回并解压：`ossutil64 cp oss://bucket/mysql-backups/file.sql.gz ./ && gunzip file.sql.gz && mysql -u root -p < file.sql`
3. 强烈建议在恢复脚本上做一次恢复演练（定期验证备份可用性）。

------

## 5002.7.6 常见运维提示（总结性的快速参考）

- **Secrets**：生产强烈建议使用 SealedSecrets / HashiCorp Vault / External Secrets。不要把 secrets 放在 Helm chart 或 Git。
- **TLS**：推荐用 `cert-manager` + Let's Encrypt（或者使用阿里云负载均衡的证书），`values-prod.yaml` 中的 `ingress.tls.secretName` 与 cert-manager `ClusterIssuer` 配合。
- **监控**：确保 jeecg 后端暴露 `actuator/prometheus`（或 Micrometer）供 Prometheus 抓取。metrics 可更精确触发 HPA（基于 custom metrics）。
- **日志**：生产中用 Loki 或 ELK 集中存储；不要把日志写到容器文件系统而不收集。
- **备份恢复**：定期做恢复演练，并保留足够的备份历史（至少 7 天或按 RPO/RTO 要求）。
- **K8s 节点**：至少 3 节点生产环境以避免单点故障；或使用云托管的 ACK（阿里云容器服务）。
