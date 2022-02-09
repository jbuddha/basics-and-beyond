---
title: Securing Api Gateway With Api Keys
slug: securing-api-gateway-api-keys
date: 2022-02-08T06:36:44.000Z
draft: true
lastmod: '2022-02-09T19:34:03.919Z'
---

<div id="myDiv" style="align-items: center; justify-content: center; display: flex"></div>
<script>
getNode = (draw, text) => {
  var text = draw.plain(text)
  text.attr({dx: 22, dy: 65})
  text.font({size: 50, family: 'Helvetica'})
  var circle = draw.circle(100).attr({ fill: '#f06' })
  var group = draw.group()
  group.add(circle)
  group.add(text)
  return { g: group, c: circle }
}
SVG.on(document, 'DOMContentLoaded', function() {
    var draw = SVG().addTo('#myDiv').size(450, 450)
    let n53 = getNode(draw, '53')
    let n51 = getNode(draw, '51')
    var timeline = new SVG.Timeline()
    n51.g.mouseover(function() { this.timeline().pause() })
    n51.g.mouseout(function() { this.timeline().play() })
    n51.g.timeline(timeline)
    // n51.g.animate(0, 0, 'absolute').move(300, 300)
    n51.g
      .animate(0, 2500).move(300, 0)
      .animate().move(300, 300)
      .animate().move(0, 300)
      .animate().move(0, 0).....
    n51.c.click(function() {
        this.fill({ color: '#eee' })
    })
})
</script>