```chartsview
#-----------------#
#- chart type    -#
#-----------------#
type: Radar

#-----------------#
#- chart data    -#
#-----------------#
data:
  - item: "The ability to discover client-Side vulnerabilities"
    user: "Scanner Effectivness"
    score: 100
  - item: "The ability to discover Server-Side vulnerabilities"
    user: "Scanner Effectivness"
    score: 50
  - item: "Speed comparing to other scanners"
    user: "Scanner Effectivness"
    score: 60
  - item: "How much this scanner can replace a pentester"
    user: "Scanner Effectivness"
    score: 40
  - item: "The ability to escape from the WAF"
    user: "Scanner Effectivness"
    score: 60

#-----------------#
#- chart options -#
#-----------------#
options:
  xField: "item"
  yField: "score"
  seriesField: "user"
  meta:
    score:
      alias: "Score"
      min: 0
      nice: true
  xAxis:
    line: null
    tickLine: null
  yAxis:
    label: false
    grid:
      alternateColor: "rgba(0, 0, 0, 0.1)"
  point: {}
  area: {}
```

