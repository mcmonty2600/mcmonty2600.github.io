---
layout: post
title: "HTML/CSS/JavaScript X-Wing Targeting Computer"
date: 2016-11-27
excerpt: A Javascript exercise of xwing pilot-assist technology
---

<div style="position:relative" markdown="1">

*You've switched off your targeting computer, what's wrong?!?*

This is an exercise in Javascript to create the "targeting computer" that the x-wing pilots use to visualize their position in the trenches. It closes in on some final posistion but does not include the thermal exhaust port.

I wonder what kind of hardware and software was used for the original prop, as seen in the movie?

[Link to Gist](https://gist.github.com/mcmonty2600/3b31674e8896ef24e9d31afaf796bbaf).

</div>

<style>
#tube_container {
  position:relative;
  height:500px;
  width: 700px;
}

.tube {
  position: absolute;
  color: rgba(240, 210, 0, 1);
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
  background: -webkit-radial-gradient(center center, contain, rgba(150,140,100,1), rgba(10,5,2,1)) center center no-repeat, black;
  opacity:0.2;
}

#gridCanvas
{
  background-color: rgba(10, 5, 2, 1);
}
</style>

<div id="tube_container">
  <canvas class = "tube" id="gridCanvas" height="400" width="640"></canvas>
  <div class="tube glow" height="400" width="640"></div>
  <div class ="tube outline" height="400" width="640"></div>
</div>

<script>
(function() {

  var c=document.getElementById("gridCanvas");
  var ctx=c.getContext("2d");
  var w = ctx.canvas.width;
  var h = ctx.canvas.height;
  var cx = w/2;
  var cy = h/2;
  var theta_mid = Math.atan((h/2)/(w/2)); // angle to the corner of the canvas
  var thetas = [0.25, 0.66, 0.9]; // angles of perspective lines
  dx = Math.cos(thetas[2]);
  dy = Math.sin(thetas[2]);
  var staticImage;
  var gridColor = 'rgba(240, 210, 0, 1)'
  var distanceIndicatorColor = 'rgba(200, 0, 0, 1)'

  // Draw the diagonal "perspective lines"
  ctx.strokeStyle = gridColor;
  ctx.lineWidth = 4;
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
  // Up to here the image doesn't change frame-to-frame. So take a snapshot and refdraw in render loop.
  var staticImage = ctx.getImageData(0,0,w,h);

  // Vertical wall-lines
  const VWALL_START = 10;
  const VWALL_X_CREATE_NEW = 30; // Create a new vertical wall-line when the last wall-line reaches this x
  const VWALL_SPEED_FACTOR = 0.125;
  var vline_arr_x = [VWALL_START]; // Array of vertical wall-line x-positions
  // Red "distance-to-target" vertical lines
  var dtt_line_x = w/2;

  setInterval(render_loop, 33); /*30 FPS*/

  function render_loop ()
  {
    ctx.clearRect(0, 0, w, h);
    ctx.putImageData(staticImage,0,0);

    // Draw the vertical wall lines.
    ctx.strokeStyle = gridColor;
    ctx.lineWidth = 4;

    if (vline_arr_x[0] > VWALL_X_CREATE_NEW) // Once the last wall-line hits this point, create a new wall-line
    {
      vline_arr_x.unshift(VWALL_START);
    }
    for (i=0; i<vline_arr_x.length;i++)
    {
      d = vline_arr_x[i];
      ctx.beginPath();
      ctx.moveTo(cx+d*dx,cy-d*dy);
      ctx.lineTo(cx+d*dx,cy+d*dy);
      ctx.lineTo(cx-d*dx,cy+d*dy);
      ctx.lineTo(cx-d*dx,cy-d*dy);
      ctx.stroke();
      vline_arr_x[i]+=VWALL_SPEED_FACTOR*vline_arr_x[i]; // the closer to the edge, the faster the line moves
      if (vline_arr_x[i]*dx>w) // vertical line is off screen
      {
        vline_arr_x.pop();
      }
    }
    // Draw the two red "distance-to-target indicator" vertical lines
    ctx.lineWidth = 8;
    ctx.strokeStyle = distanceIndicatorColor;
    ctx.beginPath();
    ctx.moveTo(cx+dtt_line_x,0);
    ctx.lineTo(cx+dtt_line_x,h);
    ctx.moveTo(cx-dtt_line_x,0);
    ctx.lineTo(cx-dtt_line_x,h);
    ctx.stroke();
    dtt_line_x -= 1;
    if (dtt_line_x <= 0)
    {
      dtt_line_x = w/2;
    }
  }

})();
</script>

<div style="position:relative" markdown="1">
## Notes

I didn't get too far before I realized there was going to be some trigonometry required to deaw these lines. I spent much time trying to recal the right trig functions! It took a some trial-and-error, and it works; but likey there are better ways.

This is implemented as an endless loop. Reay, this trench should come to an end and show the target that represents the thermal exhaust port. I didn't get around to that yet...

<script src="https://gist.github.com/mcmonty2600/3b31674e8896ef24e9d31afaf796bbaf.js"></script>

</div>
