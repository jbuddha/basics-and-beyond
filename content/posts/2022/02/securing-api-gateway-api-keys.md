---
title: Securing Api Gateway With Api Keys
slug: securing-api-gateway-api-keys
date: 2022-02-08T06:36:44.000Z
draft: true
lastmod: '2022-02-08T21:52:11.514Z'
---

<div id="myDiv" style="align-items: center; justify-content: center; display: flex"></div>
<script>
SVG.on(document, 'DOMContentLoaded', function() {
    var draw = SVG().addTo('#myDiv').size(300, 300)
    var circle = draw.circle(100).attr({ fill: '#f06' })
    var text = draw.plain('53')
    text.attr({dx: 20, dy: 65})
    text.font({size: 50})
    var group = draw.group()
    group.add(circle)
    group.add(text)
    var timeline = new SVG.Timeline()
    group.timeline(timeline)
    group.animate(0, 0, 'absolute').move(300, 300)
    group.animate(300,300, 'absolute').move(0, 0)
    circle.click(function() {
        this.fill({ color: '#eee' })
    })
})

</script>