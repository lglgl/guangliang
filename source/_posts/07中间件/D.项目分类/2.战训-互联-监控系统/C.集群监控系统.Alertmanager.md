
```shell
# 创建alertmanager配置文件/docker_data/alertmanager/config/alertmanager.yml

docker run --net host \
 -v /docker_data/alertmanager:/etc/alertmanager \
 --name prod_alertmanager_main_01 --restart always -d prom/alertmanager
 ```

### 配置文件

```shell
global:
  resolve_timeout: 1m
  wechat_api_url: 'https://qyapi.weixin.qq.com/cgi-bin/'
  wechat_api_corp_id: 'ww68b18e2e16395ab3'
  wechat_api_secret: 'NBR3ko0bdCDqtEpUpr94ag2HhV9AkEwpFXJXE6h38ao'

templates:
  - '/etc/alertmanager/template/*.tmpl'
route:
  receiver: 'wechat'
  group_by: ['env','instance','type','group','job','alertname']
  group_wait: 10s
  group_interval: 5s
  repeat_interval: 1h

receivers:
- name: 'wechat'
  wechat_configs:
  - send_resolved: true
    message: '{{ template "wechat.default.message" . }}'
    to_party: '2'
    agent_id: '1000004'
    to_user : "@all"
    api_secret: 'NBR3ko0bdCDqtEpUpr94ag2HhV9AkEwpFXJXE6h38ao'
```


### 告警模板
```shell
{{ define "wechat.default.message" }}
{{- if gt (len .Alerts.Firing) 0 -}}
{{- range $index, $alert := .Alerts -}}
{{- if eq $index 0 }}
=========测试环境监控报警 =========
告警状态：{{   .Status }}
告警级别：{{ .Labels.severity }}
告警类型：{{ $alert.Labels.alertname }}
故障主机: {{ $alert.Labels.instance }} {{ $alert.Labels.pod }}
告警主题: {{ $alert.Annotations.summary }}
告警详情: {{ $alert.Annotations.message }}{{ $alert.Annotations.description}};
触发阀值：{{ .Annotations.value }}
故障时间: {{ ($alert.StartsAt.Add 28800e9).Format "2006-01-02 15:04:05" }}
========= = end =  =========
{{- end }}
{{- end }}
{{- end }}
{{- if gt (len .Alerts.Resolved) 0 -}}
{{- range $index, $alert := .Alerts -}}
{{- if eq $index 0 }}
=========测试环境异常恢复 =========
告警类型：{{ .Labels.alertname }}
告警状态：{{   .Status }}
告警主题: {{ $alert.Annotations.summary }}
告警详情: {{ $alert.Annotations.message }}{{ $alert.Annotations.description}};
故障时间: {{ ($alert.StartsAt.Add 28800e9).Format "2006-01-02 15:04:05" }}
恢复时间: {{ ($alert.EndsAt.Add 28800e9).Format "2006-01-02 15:04:05" }}
{{- if gt (len $alert.Labels.instance) 0 }}
实例信息: {{ $alert.Labels.instance }}
{{- end }}
========= = end =  =========
{{- end }}
{{- end }}
{{- end }}
{{- end }}

```
