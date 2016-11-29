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
<script src="app.js"></script>

