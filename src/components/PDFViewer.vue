<template>
  <div id="pageContainer">
    <canvas id="the-canvas" style="border: 1px solid black; direction: ltr"></canvas>
  </div>
</template>

<script setup>
import * as pdfjsLib from "pdfjs-dist/build/pdf";
import { PDFViewer } from "pdfjs-dist/web/pdf_viewer";
import "pdfjs-dist/web/pdf_viewer.css";

import { onMounted } from "vue";

pdfjsLib.GlobalWorkerOptions.workerSrc =
  "https://cdn.jsdelivr.net/npm/pdfjs-dist@3.4.120/build/pdf.worker.min.js";

async function getPdf() {
  let loadingTask = pdfjsLib.getDocument("./pdf-sample.pdf");
  let pdf = await loadingTask.promise;
  const page = await pdf.getPage(3);
  const scale = 1.5;
  const viewport = page.getViewport({ scale });
  // Support HiDPI-screens.
  const outputScale = window.devicePixelRatio || 1;

  //
  // Prepare canvas using PDF page dimensions
  //
  const canvas = document.getElementById("the-canvas");
  const context = canvas.getContext("2d");

  canvas.width = Math.floor(viewport.width * outputScale);
  canvas.height = Math.floor(viewport.height * outputScale);
  canvas.style.width = Math.floor(viewport.width) + "px";
  canvas.style.height = Math.floor(viewport.height) + "px";

  const transform =
    outputScale !== 1 ? [outputScale, 0, 0, outputScale, 0, 0] : null;

  const renderContext = {
    canvasContext: context,
    transform,
    viewport,
  };
  page.render(renderContext);
}

onMounted(async () => {
  console.log("mounted");
  await getPdf();
});
</script>

<style>
#pageContainer {
  margin: auto;
  width: 80%;
}

div.page {
  display: inline-block;
}
</style>