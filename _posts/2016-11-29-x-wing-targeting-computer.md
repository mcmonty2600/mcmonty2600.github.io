---
layout: post
title: "HTML/CSS/JavaScript X-Wing Targeting Computer"
date: 2016-11-27
---

<div style="position:relative" markdown="1">
## You've switched off your targeting computer, what's wrong?!?

Re-watching the final x-wing assault on the Death Star in Episode IV recently, I found myself really admiring the technology we see employed in the fighters. The centerpiece technonlogy here is the "targeting computer" that the x-wing pilots use to visualize their position in the trenches and determine how far away the are from their final target, the thermal exhaust port. A sort of heads-up display that the pilot peers into, with its one-point perspective, minimalist vector-like graphics, and the feeling it gives of high speed motion, this is really the height of rebel pilot-assist technology.

Luke doesn't end up using the targeting computer for his final shot, of course. He deactivates it and decides to take out the Death Star in manual mode. Given what was at stake (entire planet) he really should have used his Jedi instincts PLUS the targeting computer. This would have maximized his chances...

Anyhow, after this YouTube-enabled nostalgia session I was inspired to see if I could work up a JavaScript version of the targeting computer itself. Here it is, and it uses just a little more code than will fit in the palm of your hand!

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

I didn't get too far before I realized there was going to be some trigonometry required. I always find myself wishing my trigonometry was more solid. I spent (way too much) time searching the Internet trying to find the right trig functions so I could calculate things like where to start drawing the vertical lines of the trench walls so they line up with the diagonal perspective lines. It took a lot of trial-and-error, and it works; but there might be better ways.

I noticed in the movie that, as you'd expect, the walls of the trench move faster as they get "closer", i.e., towards the edge of the display. I wasn't sure the proper way to do this to create realistic perspective, but I tried accelerating the horizontal lines with `x += V*x;` where v is the "velocity factor" and this seemed good enough.

This is implemented as an endless loop. In actuality, this trench comes to an end and at the end is the thermal exhaust port, visualized as a sort of target. I didn't get around to that yet...

{%3b31674e8896ef24e9d31afaf796bbaf%}
</div>
