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
  
■環境準備  
1.　Prometheus
