# hpa-custom-metrics-demo

本デモでは、OpenShift Container Platform v4にて、Prometheusのメトリクスを基にHorizontal Pod Autoscaler(HPA)の  
動作を確認するための環境の準備と実際にオートスケールの挙動を確認することが出来ます。  
  
Prometheusへメトリクスを送信するexporterを備えたnginxのイメージを使用して、nginx_http_requests_per_secondの値を  
HPAの判定基準としてオートスケールが実行されることを確認するデモです。  
  
以下のOpenShift V3.11で実施されていたものをV4で動作するように改良したものとなります。  
https://medium.com/ibm-cloud/autoscaling-applications-on-openshift-container-platform-3-11-with-custom-metrics-6e9c14474de3

・OpenShiftマニュアル  
https://access.redhat.com/documentation/ja-jp/openshift_container_platform/4.4/html-single/monitoring/index#exposing-custom-application-metrics-for-autoscaling

  
■前提条件  
・OCPのクラスターが用意出来ていること。  
・クラスター管理者権限でアクセス出来ること。  
  
環境準備
1.　hpatestというプロジェクトを作成し、exporterを備えたnginxをデプロイ  
-----------  
$ oc new-project hpatest  
$ oc apply -f app-deploy-ocpv4.yaml  
$ oc apply -f app-svc-ocpv4.yaml  
$ oc expose service/sample-app  
  
2.　カスタムPrometheusの準備とサービスモニター定義  
-----------  
Prometheus Operatorを準備  
$ oc project default  
$ oc policy add-role-to-user edit system:serviceaccount:default:prometheus-k8s -n hpatest  
$ oc apply -f create-prometheus.yaml  
$ oc apply -f create-service-monitor.yaml  
$ oc expose service/prometheus-operated  
  
3.　Prometheusアダプター作成
-----------  
$ oc apply -f create-role-sa.yaml  
$ oc apply -f create-custom-metrics.yaml  
$ oc apply -f create-api-service.yaml  
$ oc apply -f create-prometheus-adapter.yaml  
$ oc get --raw "/apis/custom.metrics.k8s.io/v1beta1/namespaces/hpatest/pods/*/nginx_http_requests_per_second" | jq .  
  
4.　カスタムメトリクスHPA定義  
-----------  
$ oc project hpatest  
$ oc apply -f custom_metrics_hpa.yaml  
  
5.　HPA動作確認
-----------  
nginxへ負荷をかける。  
### HPAでReplicas増加を確認  
$ oc get hpa  
NAME             REFERENCE               TARGETS    MINPODS   MAXPODS   REPLICAS   AGE  
custom-metrics   Deployment/sample-app   369m/70m   1         3         3          10m  
$  


