## 使用Istio构建Java微服务应用

Java + Istio on k8s/openshift
示例系统由3个简单服务构成, 组成及关系如: 客户(customer) -> 偏好(preference) -> 推荐(recommendation).
示例系统暂时使用一个简单的异常处理方案, 即 当依赖服务缺失时, 直接向用户返回错误消息.
另有两个实例用于演示istio如何处理egress route: egressgithub 和 egresshttpbin.

*目录*
* 预备工具
* 安装minishift
* 配置环境
* istio安装脚本
* 部署客户服务
* 部署偏好服务
* 部署推荐服务
* 更新重部署代码
* 监控
* 自定义监控
* 分布式追踪
* istio路由规则变化
  * 推荐: v2
* 变更istio路由规则
 * 所有用户访问推荐V2
 * 所有用户访问推荐v1
 * 所有用户访问推荐v1和v2
 * 将流量分配给v1和v2
* 错误注入?
  * http 错误 503
  * 推迟
* 重试
* 超时
* 基于user-agent头的智能路由(金丝雀部署)
  * 设置推荐使用v1
  * 设置safari用户使用v2
  * 设置mobile用户使用v2
  * 清理
* 流量监控
* 访问控制
  * 白名单
  * 黑名单
* 负载均衡器
* 流量控制
* 断路器
  * 最大连接或最大等待请求 快速失败
    * 无断路器的负载测试
    * 有断路器的负载测试
    * 清理
  * pool ejection
    * 伸缩v2部署的实例数量
    * 无失败实例的测试行为
  * 重试, 断路, pool ejection的弹性恢复
* 出口流量
  * 创建httpbin的java应用
  * 创建github的java应用
  * istio-ize 出口流量
* 提示与诀窍

## 预备工具
  minishift
  docker
  kubectl
  oc(eval $(minishift oc-env)) oc是OpenShift的主要命令
  mvn(maven)
  stern (brew install stern)
  istioctl(安装步骤里面安装)
  curl, gunzip, tar are built-in to MacOS or part of your bash shell
  git

## 安装minishift
  #!/bin/bash

  # add the location of minishift execuatable to PATH
  # I also keep other handy tools like kubectl and kubetail.sh
  # in that directory

  export MINISHIFT_HOME=~/minishift_1.12.0
  export PATH=$MINISHIFT_HOME:$PATH

  minishift profile set tutorial
  minishift config set memory 8GB
  minishift config set cpus 2
  minishift config set vm-driver virtualbox
  minishift config set image-caching true
  minishift config set registry-mirror https://z0dyr0rr.mirror.aliyuncs.com
  minishift addon enable admin-user
  minishift config set openshift-version v3.7.0
  minishift config set insecure-skip-tls-verify true
  
  minishift start

## 资料
1. istio-tutorial(https://github.com/redhat-developer-demos/istio-tutorial)
