## Basic setup

Simple template for setting up a threejs scene.

All threejs projects need these 3 mains things:
- [x] A Camera: This is what will be used to tell the render what to render (what is in view). – Projects can have multiple cameras
- [x] A scene: This is what will hold all our objects (think of this as a stage which everything needs to be in, in order to be rendered)
- [x] A renderer: This is what will draw all our objects and animations to the screen.

Other useful things which projects usually have but aren't required for threejs to work.
- [ ] An animate function: This is what will continuously call our render method to redraw or scenes (this is needed if your scene is animating).
- [ ] A resize function: This is required but is usually good to so your scene is responsive.

``` javascript
init();
animate();

init = () => {
  const camera = new THREE.PerspectiveCamera( 70, window.innerWidth / window.innerHeight, 1, 1000 );
  camera.position.z = 400;
  const scene = new THREE.Scene();
  const renderer = new THREE.WebGLRenderer();
  renderer.setPixelRatio(window.devicePixelRatio);
  renderer.setSize(window.innerWidth, window.innerHeight);

  const color = new THREE.Color('#ffffff');
  const geometry = new THREE.SphereGeometry(200, 10, 10);
  const material = new THREE.MeshBasicMaterial({ color: color });

  material.wireframe = true;

  const sphere = new THREE.Mesh(geometry, material);
  scene.add(sphere);

  document.body.appendChild(renderer.domElement);
}

onWindowResize = (event) => {
  renderer.setSize(window.innerWidth, window.innerHeight);
}

animate = () => {
  requestAnimationFrame(animate);
  render();
}

render = () => {
  renderer.render(scene, camera);
}
```

### Shaders
For an explanation of what shaders are and how to use them read [this](../shaders.md)

#### Basic shader setup
This is a basic template for setting up a html and js file for prototyping a threejs project using shaders.<br>
In this case our shader is taking a time uniform and a mouse position and using that to affect the texture.

***js***
```js
let uniforms = {
  u_time: { type: "f", value: 1.0 },
  u_resolution: { type: "v2", value: new THREE.Vector2() },
  u_mouse: { type: "v2", value: new THREE.Vector2() }
};

const material = new THREE.ShaderMaterial( {
  uniforms: uniforms,
  vertexShader: document.getElementById('vertexshader').textContent,
  fragmentShader: document.getElementById('fragmentshader').textContent,
});

document.onmousemove = (e) => {
  uniforms.u_mouse.value.x = e.pageX;
  uniforms.u_mouse.value.y = e.pageY;
}

onWindowResize = (event) => {
  renderer.setSize( window.innerWidth, window.innerHeight );
  uniforms.u_resolution.value.x = renderer.domElement.width;
  uniforms.u_resolution.value.y = renderer.domElement.height;
}

function render() {
  uniforms.u_time.value += 0.05;
  renderer.render(scene, camera);
}
```
***html***
```html
<script type="x-shader/x-vertex" id="vertexshader">
  // switch on high precision floats
  #ifdef GL_ES
  precision highp float;
  #endif
  void main() {
    gl_Position = projectionMatrix * modelViewMatrix * vec4(position,1.0);
  }
</script>

<script type="x-shader/x-fragment" id="fragmentshader">
  #ifdef GL_ES
  precision mediump float;
  #endif
  uniform vec2 u_resolution;
  uniform vec2 u_mouse;
  uniform float u_time;
  void main() {
    vec2 st = gl_FragCoord.xy/u_resolution;
    vec2 ms = 2.0*sin(u_mouse/u_resolution);
    gl_FragColor = vec4(ms.x,ms.y,st.x,1.0);
  }
</script>
``
