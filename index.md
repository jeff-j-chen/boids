<!-- test1 -->
<!DOCTYPE html>
<html><head>
    <meta charset="UTF-8">
    <title>Boids</title>
    <link href="https://fonts.googleapis.com/css?family=Gayathri&amp;display=swap" rel="stylesheet">
    <style>
        html, body {
            margin: 0;
            overflow: hidden;
            height: 100%;
        }

        canvas[resize] {
            width: 100%;
            height: 100%;
        }

        .bottomtext {
            margin: auto;
            width: 100vw;
            position: fixed;
            bottom: 0;
            color: #595959;
            text-anchor: middle;
            text-align: center;
            font-family: 'Gayathri', sans-serif;
            font-size: 14px;
            font-weight: 600;
        }

        b {
            color: #828181;
        }
    </style>
    <script type="text/javascript" src="paper-full.min.js"></script>
    <script type="text/paperscript" canvas="canvas">
    var colors=[["#461220","#8c2f39","#b23a48","#fcb9b2","#fed0bb"],["#e63946","#f1faee","#a8dadc","#457b9d","#1d3557"],["#fff275","#ff8c42","#ff3c38","#a23e48","#6c8ead"],["#ffdda1","#ffd151","#f8c537","#edb230","#e77728"],["#818479","#b5cbb7","#d2e4c4","#e4e9b2","#e7e08b"],["#e0fbfc","#c2dfe3","#9db4c0","#5c6b73","#253237"],["#114b5f","#1a936f","#88d498","#c6dabf","#f3e9d2"],["#3c4156","#14213d","#fca311","#e5e5e5","#ffffff"],["#012622","#003b36","#ece5f0","#e98a15","#59114d"],["#577590","#f3ca40","#f2a541","#f08a4b","#d78a76"],["#bce784","#5dd39e","#348aa7","#525174","#513b56"],["#0e1116","#374a67","#616283","#9e7b9b","#cb9cf2"],["#c2c1c2","#42213d","#683257","#bd4089","#f51aa4"],["#031a6b","#033860","#087ca7","#004385","#05b2dc"],["#306b34","#1c5420","#c6ffca","#77ea7f","#17b522"],["#23c9ff","#7cc6fe","#ccd5ff","#e7bbe3","#c884a6"],["#3c1518","#69140e","#a44200","#d58936","#fff94f"],["#d00000","#ffba08","#3f88c5","#032b43","#136f63"],["#ff4e00","#8ea604","#f5bb00","#ec9f05","#bf3100"],["#2d3142","#4f5d75","#bfc0c0","#ffffff","#ef8354"],["#ef798a","#f7a9a8","#7d82b8","#613f75","#e5c3d1"],["#20bf55","#0b4f6c","#01baef","#fbfbff","#757575"],["#393e41","#d3d0cb","#e2c044","#587b7f","#1e2019"],["#0a369d","#4472ca","#5e7ce2","#92b4f4","#cfdee7"],["#ffdda1","#ffbf51","#f7b036","#eda72f","#e8a127"],["#813405","#d35c13","#f9863e","#f7c3a3","#f9d7c2"],["#843636","#331313","#990303","#cc3b3b","#fc4e4e"],["#190b28","#685762","#9b9987","#efa9ae","#e55381"]];function Boid(t,o,e){this.path=new Path({strokeColor:randChoice(e),strokeWidth:"9",pivot:[-7.5,0],applyMatrix:!1,rotation:o}),this.path.add([-15,10],[0,0],[-15,-10]),this.path.position=t,this.jitter=0}Boid.prototype={moveBoid:function(){var t=new Point({angle:this.path.rotation,length:boidSpeed});this.path.position+=t,keepInView(this.path)},checkNearby:function(){var t,o=[],e=viewDist;if(boids.forEach(function(i){var a=distFormula(this.path.position,i.path.position);if(a<=viewDist){var n=(i.path.position-this.path.position).angle;n<=-135||n>=135||0!==a&&(o.push(i),a<e&&(t=i,e=a))}},this),void 0!==t)for(var i=0,a=new Point({angle:t.path.rotation,length:t.speed,applyMatrix:!1}),n=new Point({angle:this.path.rotation,length:this.speed,applyMatrix:!1});distFormula(this.path.position+n*i,t.path.position+a*i)<=distFormula(this.path.position,t.path.position)&&(i++,distFormula(this.path.position+n*i,t.path.position+a*i)<=personalSpace&&this.avoidNearby(t),!(i>maxSteps)););o.length&&(this.assimilateNearby(o),this.swerveToCenter(o))},avoidNearby:function(t){var o=this.path.position+new Point({angle:this.path.rotation,length:2*viewDist});(t.path.position.x-this.path.position.x)*(o.y-this.path.position.y)-(t.path.position.y-this.path.position.y)*(o.x-this.path.position.x)>0?this.path.rotate(rotationStrength):this.path.rotate(-rotationStrength)},assimilateNearby:function(t){var o=t.map(function(t){return t.path.rotation},this);o.length&&this.path.rotate((o.reduce(function(t,o){return t+o})/o.length-this.path.rotation)*assimStrength)},swerveToCenter:function(t){var o=t.map(function(t){return[t.path.position.x,t.path.position.y]},this),e=0,i=0;o.forEach(function(t){e+=Math.abs(t[0]),i+=Math.abs(t[1])});var a=new Point(e/t.length,i/t.length)-this.path.position;this.path.rotate((a.angle-this.path.rotation)*centerStrength)},jitterRotation:function(){this.path.rotate(this.jitter)},newJitter:function(){this.jitter=randFloat(-jitterStrength,jitterStrength)}};var boids=[],rotationStrength=1.5,jitterStrength=3,assimStrength=.1,centerStrength=.05,boidSpeed=8,personalSpace=60,numBoids=45,spawnedBoids=0,maxSteps=8,viewDist=85,colorSet=randChoice(colors),hueShift=!1,counter=-30;function onFrame(t){boids.forEach(function(t){t.jitterRotation(),t.checkNearby(),t.moveBoid()}),t.count%60==0&&boids.forEach(function(t){t.newJitter()})}function setSpawnInterval(){var t=setInterval(function(){boids.push(new Boid(view.bounds,180*randFloat(-1,1),colorSet)),++spawnedBoids>=numBoids&&clearInterval(t)},150)}function onKeyDown(t){"space"===t.key&&(project.clear(),boids.forEach(function(t){t.path.remove()}),boids=[],spawnedBoids=0,setSpawnInterval()),"control"===t.key&&(1===randint(1,5)?(hueShift=!0,boids.forEach(function(t){t.path.strokeColor=randChoice(randChoice(colors))})):(hueShift=!1,colorSet=randChoice(colors),boids.forEach(function(t){t.path.strokeColor=randChoice(colorSet)}))),"left"===t.key&&boids.forEach(function(t){t.path.rotate(-10)}),"right"===t.key&&boids.forEach(function(t){t.path.rotate(10)}),"up"===t.key&&(boidSpeed+=1),"down"===t.key&&boidSpeed>=1&&(boidSpeed-=1),"shift"===t.key&&(rotationStrength=randFloat(1,5),jitterStrength=randFloat(0,10),assimStrength=randFloat(0,.5),centerStrength=randFloat(0,.5)),"["===t.key&&boids.length&&boids.pop().path.remove(),"]"===t.key&&boids.push(new Boid([0,0],180*randFloat(-1,1),colorSet))}function keepInView(t){var o=t.position,e=view.bounds;if(!o.isInside(e)){var i=t.bounds;o.x>e.width+5&&(o.x=-t.bounds.width),o.x<-i.width-5&&(o.x=e.width),o.y>e.height+5&&(o.y=-i.height),o.y<-i.height-5&&(o.y=e.height)}}function randint(t,o){return Math.floor(Math.random()*(o-t+1)+t)}function randFloat(t,o){return Math.random()*(o-t)+t}function randChoice(t){return t[Math.floor(Math.random()*t.length)]}function distFormula(t,o){return Math.sqrt(Math.pow(t.x-o.x,2)+Math.pow(t.y-o.y,2))}setSpawnInterval();
    </script>
</head>
<body>
    <text class="bottomtext"><b>space</b> to redraw&nbsp;&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;&nbsp;<b>ctrl</b> to recolor&nbsp;&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b>] / [</b> to add / remove boids&nbsp;&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b>shift</b> to randomize variables&nbsp;&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b>up / down</b> to accelerate / decelerate&nbsp;&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b>right / left</b> to turn clockwise / counterclockwise </text>
    <canvas id="canvas" resize hidpi="off" style="background: black; -webkit-user-drag: none; user-select: none; -webkit-tap-highlight-color: rgba(0, 0, 0, 0);" width="1600" height="900"></canvas>
</body></html>
