# Deep understanding Pod

## Pod

是Kubernetes架構最小單位,  Pod內可包含多個container, Node可包含多個Pod. 每個Pod會分配虛擬IP, Container port 會映射到Pod的 Port.

## 靜態Pod

由Kubelet進行管理, 不能透過API Server進行管理, 無法與ReplicationController, DaemonSet, Deploymen進行關聯.

📋 創建靜態Pod有兩種方式

* 配置文件方式: kubelet啟動指定參數--config並指定目錄, 並將yaml放入該目錄下, 重啟kubelet.
* Http方式: kubelet啟動指定參數 --manifest-url, 自動至該資源下載yaml並解析, 創建Pod.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: static-web
  labels:
    name: static-web
spec:
  containers:
  - name: static-web
    image: nginx
    ports:
    - name: web
      containerPort: 80
```

## Pod容器共享Volume

設置Volume: app-logs, 兩個container都將內部文件映射到volume下.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: volume-pod
spec:
  containers:
  - name: tomcat
    image: tomcat
    ports:
    - containerPort: 8080
    volumeMounts:
    - name: app-logs
      mountPath: /usr/local/tomcat/logs
  - name: busybox
    image: busybox
    command: ["sh", "-c", "tail -f /logs/catalina*.log"]
    volumeMounts:
    - name: app-logs
      mountPath: /logs
  volumes:
  - name: app-logs
    emptyDir: {}
```

## Pod配置管理

### ConfigMap

以key: value形式保存於Kubernetes系統中, 是一種統一的應用配置方案.

📋 應用場景

* 生成容器環境變量
* 設置容器啟動命令的啟動次數\(須設置環境變量\)
* 以Volume掛載容器內部文件或目錄

### 創建ConfigMap資源對象

🎯 創建兩組key: value, apploglevel: info, appdatadir: /var/data

{% code title="cm-appvars.yaml" %}
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: cm-appvars
data:
  apploglevel: info
  appdatadir: /var/data
```
{% endcode %}

🎯 以文件內容當作value

{% code title="cm-appconfigfiles.yaml" %}
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: cm-appconfigfiles
data:
  key-serverxml: |
    <?xml version='1.0' encoding='utf-8'?>
    <Server port="8005" shutdown="SHUTDOWN">
      <Listener className="org.apache.catalina.startup.VersionLoggerListener" />
      <Listener className="org.apache.catalina.core.AprLifecycleListener" SSLEngine="on" />
      <Listener className="org.apache.catalina.core.JreMemoryLeakPreventionListener" />
      <Listener className="org.apache.catalina.mbeans.GlobalResourcesLifecycleListener" />
      <Listener className="org.apache.catalina.core.ThreadLocalLeakPreventionListener" />
      <GlobalNamingResources>
        <Resource name="UserDatabase" auth="Container"
                  type="org.apache.catalina.UserDatabase"
                  description="User database that can be updated and saved"
                  factory="org.apache.catalina.users.MemoryUserDatabaseFactory"
                  pathname="conf/tomcat-users.xml" />
      </GlobalNamingResources>
      <Service name="Catalina">
        <Connector port="8080" protocol="HTTP/1.1"
                   connectionTimeout="20000"
                   redirectPort="8443" />
        <Connector port="8009" protocol="AJP/1.3" redirectPort="8443" />
        <Engine name="Catalina" defaultHost="localhost">
          <Realm className="org.apache.catalina.realm.LockOutRealm">
            <Realm className="org.apache.catalina.realm.UserDatabaseRealm"
                   resourceName="UserDatabase"/>
          </Realm>
          <Host name="localhost"  appBase="webapps"
                unpackWARs="true" autoDeploy="true">
            <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
                   prefix="localhost_access_log" suffix=".txt"
                   pattern="%h %l %u %t &quot;%r&quot; %s %b" />
          </Host>
        </Engine>
      </Service>
    </Server>
  key-loggingproperties: "handlers
    = 1catalina.org.apache.juli.FileHandler, 2localhost.org.apache.juli.FileHandler,
    3manager.org.apache.juli.FileHandler, 4host-manager.org.apache.juli.FileHandler,
    java.util.logging.ConsoleHandler\r\n\r\n.handlers = 1catalina.org.apache.juli.FileHandler,
    java.util.logging.ConsoleHandler\r\n\r\n1catalina.org.apache.juli.FileHandler.level
    = FINE\r\n1catalina.org.apache.juli.FileHandler.directory = ${catalina.base}/logs\r\n1catalina.org.apache.juli.FileHandler.prefix
    = catalina.\r\n\r\n2localhost.org.apache.juli.FileHandler.level = FINE\r\n2localhost.org.apache.juli.FileHandler.directory
    = ${catalina.base}/logs\r\n2localhost.org.apache.juli.FileHandler.prefix = localhost.\r\n\r\n3manager.org.apache.juli.FileHandler.level
    = FINE\r\n3manager.org.apache.juli.FileHandler.directory = ${catalina.base}/logs\r\n3manager.org.apache.juli.FileHandler.prefix
    = manager.\r\n\r\n4host-manager.org.apache.juli.FileHandler.level = FINE\r\n4host-manager.org.apache.juli.FileHandler.directory
    = ${catalina.base}/logs\r\n4host-manager.org.apache.juli.FileHandler.prefix =
    host-manager.\r\n\r\njava.util.logging.ConsoleHandler.level = FINE\r\njava.util.logging.ConsoleHandler.formatter
    = java.util.logging.SimpleFormatter\r\n\r\n\r\norg.apache.catalina.core.ContainerBase.[Catalina].[localhost].level
    = INFO\r\norg.apache.catalina.core.ContainerBase.[Catalina].[localhost].handlers
    = 2localhost.org.apache.juli.FileHandler\r\n\r\norg.apache.catalina.core.ContainerBase.[Catalina].[localhost].[/manager].level
    = INFO\r\norg.apache.catalina.core.ContainerBase.[Catalina].[localhost].[/manager].handlers
    = 3manager.org.apache.juli.FileHandler\r\n\r\norg.apache.catalina.core.ContainerBase.[Catalina].[localhost].[/host-manager].level
    = INFO\r\norg.apache.catalina.core.ContainerBase.[Catalina].[localhost].[/host-manager].handlers
    = 4host-manager.org.apache.juli.FileHandler\r\n\r\n"
```
{% endcode %}

📋 kubectl命令創建ConfigMap

* --from-file: 目錄下每個配置文件名都會被設置為key

```yaml
kubectl create configmap NAME --from-file=config-file-dir
```

* --from-literal: 直接指定key: value

```yaml
kubectl create configmap NAME --from-literal=key1=value1 --from-literal=key2=value2
```

### 在Pod中使用ConfigMap

 1 通過環境變量方式使用ConfigMap

🧠 ConfigMap設定key: value

{% code title="cm-appvars.yaml" %}
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: cm-appvars
data:
  apploglevel: info
  appdatadir: /var/data
```
{% endcode %}

```yaml
kubectl apply -f cm-appvars.yaml
```

🎯Pod將ConfigMap設定為環境變量

{% code title="cm-test-pod-use-envvar.yaml" %}
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: cm-test-pod
spec:
  containers:
  - name: cm-test
    image: busybox
    command: [ "/bin/sh", "-c", "env | grep APP" ]
    env:
    - name: APPLOGLEVEL
      valueFrom:
        configMapKeyRef:
          name: cm-appvars
          key: apploglevel
    - name: APPDATADIR
      valueFrom:
        configMapKeyRef:
          name: cm-appvars
          key: appdatadir
  restartPolicy: Never
```
{% endcode %}

```yaml
kubectl apply -f cm-test-pod-use-envvar.yaml
```

查看該Pod log

```bash
kubectl logs cm-test-pod
```

{% hint style="info" %}
🧙♂進階用法透過envFrom, 將Config所有定義key: value自動生成環境變數 
{% endhint %}

{% code title="cm-test-pod-envfrom.yaml" %}
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: cm-test-pod
spec:
  containers:
  - name: cm-test
    image: busybox
    command: [ "/bin/sh", "-c", "env" ]
    envFrom:
    - configMapRef:
        name: cm-appvars
  restartPolicy: Never
```
{% endcode %}

2 通過volumeMount使用ConfigMap

通過`cm-appconfigfiles.yaml`將文件作為ConfigMap, 再將ConfigMap已文件形式mount到容器內

{% code title="cm-test-pod-volume.yaml" %}
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: cm-test-app
spec:
  containers:
  - name: cm-test-app
    image: kubeguide/tomcat-app:v1
    ports:
    - containerPort: 8080
    volumeMounts:
    - name: serverxml
      mountPath: /configfiles
  volumes:
  - name: serverxml
    configMap:
      name: cm-appconfigfiles
      items:
      - key: key-serverxml
        path: server.xml
      - key: key-loggingproperties
        path: logging.properties
```
{% endcode %}

