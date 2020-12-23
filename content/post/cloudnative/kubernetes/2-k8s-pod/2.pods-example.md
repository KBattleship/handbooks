# 1.SpringBoot--Pods项目初体验

> 在SpringBoot项目中通过fabric8打包插件构建docker镜像


通过Kubernetes的接口请求Pod对象，相信信息如下：
```json
{
  "kind": "Pod",
  "apiVersion": "v1",
  "metadata": {
    "name": "kubernetes-hello-world-779c4c748b-2rv27",
    "generateName": "kubernetes-hello-world-779c4c748b-",
    "namespace": "default",
    "selfLink": "/api/v1/namespaces/default/pods/kubernetes-hello-world-779c4c748b-2rv27",
    "uid": "dea46f9f-cd4b-11e9-b38e-025000000001",
    "resourceVersion": "34209",
    "creationTimestamp": "2019-09-02T06:35:35Z",
    "labels": {
      "app": "kubernetes-hello-world",
      "group": "org.springframework.cloud",
      "pod-template-hash": "779c4c748b",
      "provider": "fabric8",
      "version": "1.1.0.M2"
    },
    "annotations": {
      "fabric8.io/docs-url": "scp://static.springframework.org/var/www/domains/springframework.org/static/htdocs/spring-cloud/docs/kubernetes-hello-world/1.1.0.M2/spring-cloud-kubernetes/spring-cloud-kubernetes-examples/kubernetes-hello-world",
      "fabric8.io/iconUrl": "img/icons/spring-boot.svg",
      "fabric8.io/metrics-path": "dashboard/file/kubernetes-pods.json/?var-project=kubernetes-hello-world\u0026var-version=1.1.0.M2",
      "fabric8.io/scm-con-url": "scm:git:git://github.com/spring-cloud-incubator/spring-cloud-kubernetes.git/spring-cloud-kubernetes-examples/kubernetes-hello-world",
      "fabric8.io/scm-devcon-url": "scm:git:ssh://git@github.com/spring-cloud-incubator/spring-cloud-kubernetes.git/spring-cloud-kubernetes-examples/kubernetes-hello-world",
      "fabric8.io/scm-tag": "HEAD",
      "fabric8.io/scm-url": "https://github.com/spring-cloud-incubator/spring-cloud-kubernetes/spring-cloud-kubernetes-examples/kubernetes-hello-world"
    },
    "ownerReferences": [
      {
        "apiVersion": "apps/v1",
        "kind": "ReplicaSet",
        "name": "kubernetes-hello-world-779c4c748b",
        "uid": "dea3c6a5-cd4b-11e9-b38e-025000000001",
        "controller": true,
        "blockOwnerDeletion": true
      }
    ]
  },
  "spec": {
    "volumes": [
      {
        "name": "default-token-b4crd",
        "secret": {
          "secretName": "default-token-b4crd",
          "defaultMode": 420
        }
      }
    ],
    "containers": [
      {
        "name": "spring-boot",
        "image": "cloud/kubernetes-hello-world:1.1.0.M2",
        "ports": [
          {
            "name": "http",
            "containerPort": 8080,
            "protocol": "TCP"
          },
          {
            "name": "prometheus",
            "containerPort": 9779,
            "protocol": "TCP"
          },
          {
            "name": "jolokia",
            "containerPort": 8778,
            "protocol": "TCP"
          }
        ],
        "env": [
          {
            "name": "KUBERNETES_NAMESPACE",
            "valueFrom": {
              "fieldRef": {
                "apiVersion": "v1",
                "fieldPath": "metadata.namespace"
              }
            }
          }
        ],
        "resources": {
          
        },
        "volumeMounts": [
          {
            "name": "default-token-b4crd",
            "readOnly": true,
            "mountPath": "/var/run/secrets/kubernetes.io/serviceaccount"
          }
        ],
        "livenessProbe": {
          "httpGet": {
            "path": "/",
            "port": 8080,
            "scheme": "HTTP"
          },
          "initialDelaySeconds": 180,
          "timeoutSeconds": 1,
          "periodSeconds": 10,
          "successThreshold": 1,
          "failureThreshold": 3
        },
        "readinessProbe": {
          "httpGet": {
            "path": "/",
            "port": 8080,
            "scheme": "HTTP"
          },
          "initialDelaySeconds": 10,
          "timeoutSeconds": 1,
          "periodSeconds": 10,
          "successThreshold": 1,
          "failureThreshold": 3
        },
        "terminationMessagePath": "/dev/termination-log",
        "terminationMessagePolicy": "File",
        "imagePullPolicy": "IfNotPresent",
        "securityContext": {
          "privileged": false,
          "procMount": "Default"
        }
      }
    ],
    "restartPolicy": "Always",
    "terminationGracePeriodSeconds": 30,
    "dnsPolicy": "ClusterFirst",
    "serviceAccountName": "default",
    "serviceAccount": "default",
    "nodeName": "docker-desktop",
    "securityContext": {
      
    },
    "schedulerName": "default-scheduler",
    "tolerations": [
      {
        "key": "node.kubernetes.io/not-ready",
        "operator": "Exists",
        "effect": "NoExecute",
        "tolerationSeconds": 300
      },
      {
        "key": "node.kubernetes.io/unreachable",
        "operator": "Exists",
        "effect": "NoExecute",
        "tolerationSeconds": 300
      }
    ],
    "priority": 0,
    "enableServiceLinks": true
  },
  "status": {
    "phase": "Running",
    "conditions": [
      {
        "type": "Initialized",
        "status": "True",
        "lastProbeTime": null,
        "lastTransitionTime": "2019-09-02T06:35:35Z"
      },
      {
        "type": "Ready",
        "status": "True",
        "lastProbeTime": null,
        "lastTransitionTime": "2020-01-17T01:47:07Z"
      },
      {
        "type": "ContainersReady",
        "status": "True",
        "lastProbeTime": null,
        "lastTransitionTime": "2020-01-17T01:47:07Z"
      },
      {
        "type": "PodScheduled",
        "status": "True",
        "lastProbeTime": null,
        "lastTransitionTime": "2019-09-02T06:35:35Z"
      }
    ],
    "hostIP": "192.168.65.3",
    "podIP": "10.1.0.49",
    "startTime": "2019-09-02T06:35:35Z",
    "containerStatuses": [
      {
        "name": "spring-boot",
        "state": {
          "running": {
            "startedAt": "2020-01-17T01:46:50Z"
          }
        },
        "lastState": {
          "terminated": {
            "exitCode": 255,
            "reason": "Error",
            "startedAt": "2020-01-16T09:29:52Z",
            "finishedAt": "2020-01-17T01:46:30Z",
            "containerID": "docker://ec8892c9949c0f226636d68c0f0f9b675b60693011d510f3a218e579fe43f992"
          }
        },
        "ready": true,
        "restartCount": 8,
        "image": "cloud/kubernetes-hello-world:1.1.0.M2",
        "imageID": "docker://sha256:559a7fbc5858f9b3ca2bb9ada10235dbc169846e988a46a80c8ab123560ea168",
        "containerID": "docker://af0de58af170361c986110e79eeca49b85c0eef23ae688f52b23b5e95111fbe0"
      }
    ],
    "qosClass": "BestEffort"
  }
}
```