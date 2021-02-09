# IBM Devops 实践

本章主要是针对 

1.IBM Devops 工具链使用。

2.IBM Toolkit 实践（Katacode快速构建版本）。

3.Tekton 部署使用实践。

## 第一部分 IBM Devops 工具链使用

IBM Devops 提供的 Devops 服务分成两种：

基于 IBM Cloud 云服务底座提供的 [IBM Cloud Devops](https://www.ibm.com/cloud/devops)

混合云环境下的 [IBM Z Enterprise DevOps](https://www.ibm.com/it-infrastructure/z/capabilities/enterprise-devops)

### IBM Cloud Devops

IBM Cloud Devops 提供了开放的**工具链**以及很多符合各种企业需求的**工具链模板**，可自动构建和部署应用程序。

提供实现DevOps和DevSecOps方法的各种开源工具，通过创建支持开发，部署和操作任务的简单部署工具链来开始使用。

Delivery Pipeline （交付管道）是 IBM Devops 提供的工具链管理方式，交付包括以最少人工干预的可重复方式构建、测试和部署的交付管道。在管道中，阶段序列检索输入并运行作业，如构建、测试和部署。这里有两种管理方式，经典方式和**Tekton**方式。

#### 工具链

工具链提供了一组集成的工具，用于构建、部署和管理应用程序。您可以创建工具链以包含 IBM Cloud 服务、开放式源代码工具和第三方工具，**自由组装**，使开发和操作**可重复**并易于管理。

![工具链](./ibm-devops-toolchain-tools.png)
<center>图：IBM Cloud Devops 工具链工具</center>

开源工具内容涵盖了 Devops 实践内容：

- 项目管理和敏捷研发：

  **JIRA** 项目管理和跟踪
  
  **GitHub / GotLab / Bitbucket** 代码仓库
  
  **Slack** 项目协调和协作
  
  **Eclipse Orion Web IDE** 基于浏览器的IDE

- 持续交付

  **Jenkins** 持续集成
  
  **SonarQube** 代码质量检查
  
  **Sauce Labs** 自动执行项目的持续集成测试
  
  **Artifactory / Nexus** 构建制品存储
  
- 持续部署

  **Delivery Pipeline** 构建和部署 （必须，工具链通过 Delivery Pipeline 管理）
  
  **PagerDuty 当 Delivery Pipeline** 失败时发送警告

- 安全

  **Key Protect / HashiCorp Vault** 管理工具链私钥
  
- 监控和度量

  **无**
  
- 其他工具

  用户自定义方式集成工具，配置工具图标和工具实例 URL 使用其他工具集成到工具链中
  
##### 工具链集成

IBM Devops 工具集成相对简单，针对系统提供的这19种集成工具，可以使用公有部署和私有部署的集成能力，例如使用公有 GitLab 或私有的 GitLab ，而私有的集成方式基本使用 API 方式添加工具服务，并且在 IBM Cloud 中支持安装 CLI 来管理工具。

例如SonarQube工具集成：
![SonarQube工具集成](./ibm-devops-toolchain-sonarqube.png)
<center>图：IBM Cloud Devops SonarQube集成</center>

#### 工具链模板

提供快速建立适用于用户场景的工具链。

![工具链模板](./ibm-devops-toolchain-module.png)
<center>图：IBM Cloud Devops 工具链模板</center>

### 对比 CODING 的优势
- CODING 提供的是一套相对固定的工具链，只能增减。
  IBM Devops 提出的工具链理念，提供各种开源 Devops 行业流行工具，可以更加灵活搭配使用。
  并且对外开放工具链添加接口，用户可以随意添加自己生产线上存在的工具。
  
- IBM Devops 与 IBM Cloud 相对于 CODING 与腾讯云关联性更强。在云服务提供的IaaS，PaaS服务基础上，使其Saas服务能无缝链接。
  同时也是跟 IBM Cloud 强绑定，如果不使用 IBM 提供的云资源，很多资源非开源，很难开展试用。

## 第二部分 IBM Toolkit 实践

IBM Toolkit 是 IBM Devops 提供的 Devops 开源工具的集合，具有以下特性：

**集群**：既托管工具又本身是应用程序构建的部署目标的Red Hat OpenShift或Kubernetes集群。

**后端服务**：原生云应用程序通常需要的云服务，用于监视，安全性和持久性。

**CI / CD**：预先构建的，可立即运行的持续交付管道，结合了一流的开源软件工具。

**入门工具包**：适用于常见应用程序组件和任务的预构建代码模板，结合了开发人员可以根据需要添加到其代码库中的最佳实践。

**仪表板**：集中式控制台，可帮助开发人员使用环境的功能。

### IBM Toolkit 安装

**安装环境**：OpenShift Origin是红帽基于开源的云平台，允许开发人员构建，测试和部署云应用。封装了原生 Kubernets ，集成 EFK 实现应用程序日志收集功能，Prometheus做系统监控。在 IBM Toolkit实践中区别 K8s 最重要的提升是使用基于 **RBAC 体系管理用户权限**，使集成进来的 Devops 工具自动继承 OpenShift 平台的用户权限管理，在CLI 和dashboard上同时支持。

#### Openshift 和 Kubernetes 对比

基本参数和能力对比：
|  | Kubernetes | OpenShift |
| :----: | :----: | :----: |
| 开发商 | 云原生计算基金会（CNCF） | 红帽（Rad Hat） |
| 开发语言 | Go | Go，Angular |
| 类型 | 集群管理软件 | 云计算，Paas |
| 使用 | 开源 | 开源 |
| CLI | kubectl | oc |
| 用户权限对接 | 接口支持 | 接口支持，多角色多用户管理，同时 CLI 和 Dashboard 支撑，易用性强 |
| 项目管理功能 | namespace | 在namespace基础上，增加project管理，同时 CLI 和 Dashboard 支撑，易用性更强 |
| CI / CD | 需要第三方继承支持 | 自身集成包括容器管理，原生Jenkins Pipeline |
| 日志监控 | Heapster | 集成EFK日志收集，Prometheus系统监控 |
| 版本控制 | 暂无 | 内置Git Server（IBM Cloud Devops提供了GitOps能力） |
| 负载均衡 | Ingress/kube-proxy | OpenShift HAProxy Router |

1.用户权限管理
OpenShift 和 Kubernetes 都提供针对例如Ldap的接口和众多认证系统对接。但在易用性上，Open'Shift


这里使用 [Katacode 试用环境安装](https://learn.openshift.com/playgrounds/openshift45/)，以下所有步骤都在 Katacode 提供的实验环境执行。

![Katacode for Openshift](./openshift-install-katacode.png)

1. 注册公钥登录github，把公钥加入github，用于拉取toolkit工具包以及后续CI实践。
```shell
ssh-keygen -t rsa -b 4096 -C "amiesky@sina.cn"
cat ~/.ssh/id_rsa.pub
```
![Add ssh in GitHub](./openshift-install-add-ssh.png)

2. 下载toolkit安装包，并执行安装。
```shell
curl -LO https://raw.githubusercontent.com/ibm-garage-cloud/ibm-garage-iteration-zero/master/install/install-ibm-toolkit.yaml
oc create -f ./ibm-garage-iteration-zero/install/install-ibm-toolkit.yaml
sleep 5
oc wait pod -l job-name=ibm-toolkit --for=condition=Ready -n default
oc logs job/ibm-toolkit -f -n default
```
![Toolkit install](./toolkit-install-i.png)
<center>toolkit安装执行</center>

![toolkit install complete](./toolkit-install-complete.png)
<center>toolkit 安装完成</center>

3. 如果需要使用使用ibmcloud命令，可以通过CLI形式安装
```shell
curl -fsSL https://clis.cloud.ibm.com/install/linux | sh
ibmcloud --version
```
![ibmcloud install](./ibmcloud-install.png)
<center>ibmcloud 安装</center>

4. igc 命令安装
```shell
npm i -g @ibmgaragecloud/cloud-native-toolkit-cli
igc --version
```

实践过程中用到的命令指南：
|  | kubectl | oc | ibmcloud | igc |
| :----: | :----: | :----: | :----: | :----: |
| 系统 | Kubernetes | OpenShift | IBM Cloud | IBM Garage for Cloud（Cloud Native Toolkit CLI） |
| 应用 | 容器管理 | 容器管理及OpenShift新增功能 | IBM 云账号相关操作 | IBM Toolkit工具 |


5. toolkit部署成功后，可以在openshift dashboard后台看到添加的toolkit开源服务。
![openshift dashboard](./openshift-dashboard-i.png)

### IBM Toolkit 使用

IBM Toolkit 提供的开源工具跟IBM 工具链中提供的工具基本一致。

1.使用CLI命令创建新项目myproject，默认会跳转至新创建的项目下。
```shell
oc new-project myproject
```

2.使用toolkit提供的模板项目创建GitHub工程。

3.


## 第三部分 Tekton 安装及实践

![Tekton](https://tekton.dev/) 是一款功能非常强大而灵活的 CI/CD 开源的云原生框架。Tekton 的前身是 Knative 项目的 build-pipeline 项目，这个项目是为了给 build 模块增加 pipeline 的功能，但是随着不同的功能加入到 Knative build 模块中，build 模块越来越变得像一个通用的 CI/CD 系统，于是，索性将 build-pipeline 剥离出 Knative，就变成了现在的 Tekton，而 Tekton 也从此致力于提供全功能、标准化的云原生 CI/CD 解决方案。

### Tekton 安装

1. 在K8s集群中安装Tekton，执行Tekton资源文件。
```shell
kubectl apply -f https://github.com/tektoncd/pipeline/releases/download/v0.12.0/release.yaml
```
![Tekton install](./tekton-install.png)

资源文件安装成功后，会生成tekton-pipelines命名空间，里面存在Tekton相关资源对象。
![Tekton install pods](./tekton-install-i.png)

2. 如果需要使用命令行操作，可以安装 Tekton CLI 工具。
```shell

```

3. 同时，可以安装 Tekton Dashboard ，通过 Dashboard 查看 Tekton 整个任务的构建过程。
```shell
$ kubectl apply -f kubectl apply -f https://github.com/Aimee-Song/ibm-devops.github.io/blob/gh-pages/tekton-dashboard.yaml
```
![Tekton Dashboard Install Result](./tekton-dashboard-i.png)
