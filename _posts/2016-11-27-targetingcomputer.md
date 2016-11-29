# Test

<style>
html,body {
  color: rgba(240, 210, 0, 1);
  margin:0;
  background-color: rgba(5, 5, 5, 1);
}

.tube {
  position: absolute; /*allows div to overlap */
  left: 50px;
  top: 40px;
  width: 680px;
  height: 400px;
  border-radius: 40px;
}

.outline {
  box-sizing: border-box;
  border-style:solid;
  border-width: 6px;
}

.glow {
  z-index:-1;
  background: -webkit-radial-gradient(center center, contain, rgba(150,140,100,1), rgba(10,5,2,1)) center center no-repeat, black;
  opacity:0.2;
}
<style>
<div class="tube glow" height="400" width="640"></div>
<div class ="tube outline" height="400" width="640"></div>
<canvas class = "tube" id="gridCanvas" height="400" width="640"></canvas>
<script>

(function() {

  var c=document.getElementById("gridCanvas");
  var ctx=c.getContext("2d");
  var w = ctx.canvas.width;
  var h = ctx.canvas.height;
  var cx = w/2;
  var cy = h/2;
  /*
  var offset1 = 0.2*h;  // endpoint of perspective line #1 on outer vertical edge of canvas
  var offset2 = 0.43*w; // endpoint of perspective line #2 on outer horizontal edge of canvas
  var offset3 = 0.25*w; // endpoint of perspective line #3 on outer horizontal edge of canvas
  var theta1 = Math.atan((h/2)/offset3);
  */
  var theta_mid = Math.atan((h/2)/(w/2)); // angle to the corner of the canvas
  var thetas = [0.25, 0.66, 0.9]; // angles of perspective lines
  dx = Math.cos(thetas[2]);
  dy = Math.sin(thetas[2]);
  var staticImage;
  var gridColor = 'rgba(240, 210, 0, 1)'
  var distanceIndicatorColor = 'rgba(200, 0, 0, 1)'

  ctx.strokeStyle = gridColor;
  ctx.lineWidth = 4;

  // diagonal lines
  /*
  var endpoints = [{x:w,y:cy+offset1}, {x:0,y:cy+offset1},{x:w,y:cy-offset1},{x:0,y:cy-offset1}];
  endpoints = endpoints.concat([ {x:cx+offset2,y:h}, {x:cx-offset2,y:h},{x:cx+offset2,y:0},{x:cx-offset2,y:0} ])
  endpoints = endpoints.concat([ {x:cx+offset3,y:h}, {x:cx-offset3,y:h},{x:cx+offset3,y:0},{x:cx-offset3,y:0} ])
  ctx.beginPath();
  for(i=0; i<endpoints.length;i++)
  {
    ctx.moveTo(cx,cy);
    ctx.lineTo(endpoints[i].x, endpoints[i].y);
    ctx.stroke();
  }
  */

  ctx.beginPath();
  for(i=0; i<3;i++)
  {
    if (thetas[i] < theta_mid) {
      ctx.moveTo(cx,cy);
      ctx.lineTo(w, cy - (w/2)*Math.tan(thetas[i]));
      ctx.moveTo(cx,cy);
      ctx.lineTo(w, cy + (w/2)*Math.tan(thetas[i]));
      ctx.moveTo(cx,cy);
      ctx.lineTo(0, cy + (w/2)*Math.tan(thetas[i]));
      ctx.moveTo(cx,cy);
      ctx.lineTo(0, cy - (w/2)*Math.tan(thetas[i]));
      ctx.stroke();
    }
    else {
      ctx.moveTo(cx,cy);
      ctx.lineTo(cx+(h/2)/Math.tan(thetas[i]), h);
      ctx.moveTo(cx,cy);
      ctx.lineTo(cx-(h/2)/Math.tan(thetas[i]), h);
      ctx.moveTo(cx,cy);
      ctx.lineTo(cx+(h/2)/Math.tan(thetas[i]), 0);
      ctx.moveTo(cx,cy);
      ctx.lineTo(cx-(h/2)/Math.tan(thetas[i]), 0);
      ctx.stroke();
    }
  }


  var staticImage = ctx.getImageData(0,0,w,h);

  // setup for render loop
  var darr = [10];
  var distanceIndicatorLinePos = w/2;
  setInterval(render_loop, 33); /*30 FPS*/

  function render_loop ()
  {
    ctx.clearRect(0, 0, w, h);
    ctx.putImageData(staticImage,0,0);
    ctx.strokeStyle = gridColor;
    ctx.lineWidth = 4;
    if (darr[0] > 30)
    {
      darr.unshift(10);
    }
    for (i=0; i<darr.length;i++)
    {
      d = darr[i];
      ctx.beginPath();
      ctx.moveTo(cx+d*dx,cy-d*dy);
      ctx.lineTo(cx+d*dx,cy+d*dy);
      ctx.lineTo(cx-d*dx,cy+d*dy);
      ctx.lineTo(cx-d*dx,cy-d*dy);
      ctx.stroke();
      darr[i]+=0.125*darr[i]; // the closer to the edge, the faster the line moves
      if (darr[i]*dx>w) // off screen
      {
        darr.pop();
      }
    }
    ctx.lineWidth = 8;
    ctx.strokeStyle = distanceIndicatorColor;
    ctx.beginPath();
    ctx.moveTo(cx+distanceIndicatorLinePos,0);
    ctx.lineTo(cx+distanceIndicatorLinePos,h);
    ctx.moveTo(cx-distanceIndicatorLinePos,0);
    ctx.lineTo(cx-distanceIndicatorLinePos,h);
    ctx.stroke();
    distanceIndicatorLinePos -= 1;
    if (distanceIndicatorLinePos == 0)
    {
      distanceIndicatorLinePos = w/2;
    }
  }

})();
</script>

