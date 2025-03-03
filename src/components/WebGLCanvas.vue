<script setup>
import { onMounted, ref } from "vue";

const canvasRef = ref(null);

onMounted(() => {
  const canvas = canvasRef.value;
  const gl = canvas.getContext("webgl");

  if (!gl) {
    alert("WebGL not supported!");
    return;
  }

  // Resize canvas to fit the screen
  function resizeCanvas() {
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
    gl.viewport(0, 0, canvas.width, canvas.height);
  }
  window.addEventListener("resize", resizeCanvas);
  resizeCanvas();

  // Vertex shader
  const vertexShaderSource = `
    attribute vec2 position;
    void main() {
      gl_Position = vec4(position, 0.0, 1.0);
    }
  `;

  // Fragment shader
  const fragmentShaderSource = `
    precision mediump float;
    void main() {
      gl_FragColor = vec4(0.3, 0.8, 0.9, 1.0); // Light teal color
    }
  `;

  function createShader(gl, type, source) {
    const shader = gl.createShader(type);
    gl.shaderSource(shader, source);
    gl.compileShader(shader);
    return shader;
  }

  const vertexShader = createShader(gl, gl.VERTEX_SHADER, vertexShaderSource);
  const fragmentShader = createShader(gl, gl.FRAGMENT_SHADER, fragmentShaderSource);

  const program = gl.createProgram();
  gl.attachShader(program, vertexShader);
  gl.attachShader(program, fragmentShader);
  gl.linkProgram(program);
  gl.useProgram(program);

  // Create a simple triangle
  const vertices = new Float32Array([
    -0.5, -0.5,
    0.5, -0.5,
    0.0,  0.5
  ]);

  const buffer = gl.createBuffer();
  gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
  gl.bufferData(gl.ARRAY_BUFFER, vertices, gl.STATIC_DRAW);

  const positionLocation = gl.getAttribLocation(program, "position");
  gl.enableVertexAttribArray(positionLocation);
  gl.vertexAttribPointer(positionLocation, 2, gl.FLOAT, false, 0, 0);

  function draw() {
    gl.clearColor(0.0, 0.0, 0.0, 1.0);
    gl.clear(gl.COLOR_BUFFER_BIT);
    gl.drawArrays(gl.TRIANGLES, 0, 3);
    requestAnimationFrame(draw);
  }

  draw();
});
</script>

<template>
  <canvas ref="canvasRef"></canvas>
</template>

<style>
canvas {
  display: block;
  width: 400px;
  height: 400px;
}
</style>
