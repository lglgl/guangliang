


{location="$location"} 

(1 - avg(rate(node_cpu_seconds_total{origin_prometheus=~"$origin_prometheus",job=~"$job",mode="idle"}[$interval])) by (instance)) * 100