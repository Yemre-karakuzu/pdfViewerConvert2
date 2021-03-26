<template>
  <div id="pageContainer">
    <button @click="currentPagePrev" class="button">Prev</button>

    <span class="textspan"> {{ currentPage }} / {{ pageCount }} </span>
    <button @click="currentPageNext" class="button">Next</button>
    <canvas id="the-canvas"></canvas>
  </div>
</template>

<script>
import "pdfjs-dist/web/pdf_viewer.css";
const pdfjsLib = require("pdfjs-dist");
pdfjsLib.GlobalWorkerOptions.workerSrc = require("pdfjs-dist/build/pdf.worker.entry.js");
export default {
  name: "PdfViewer",
  data() {
    return {
      currentPage: 1,
      pageCount: null,
    };
  },
  created() {
    this.getPdf();
  },
  methods: {
    async getPdf() {
      let currentPageNumber = this.currentPage;
      let loadingTask = pdfjsLib.getDocument("./sample.pdf");
      let pdfto = loadingTask.promise;
      this.pageCount = (await pdfto).numPages;
      loadingTask.promise.then((pdf) => {
        pdf.getPage(currentPageNumber).then((page) => {
          var scale = 2.5;
          var viewport = page.getViewport({ scale: scale });

          var canvas = document.getElementById("the-canvas");
          var context = canvas.getContext("2d");
          canvas.height = viewport.height;
          canvas.width = viewport.width;

          var renderContext = {
            canvasContext: context,
            viewport: viewport,
          };
          page.render(renderContext);
        });
      });
    },
    currentPageNext() {
      if (this.pageCount > this.currentPage) {
        this.currentPage++;
        this.getPdf();
      }
    },
    currentPagePrev() {
      if (this.currentPage > 1) {
        this.currentPage--;
        this.getPdf();
      }
    },
  },
};
</script>

<style>
#pageContainer {
  margin: auto;
}
.button {
  color: blue;
  background: white;
  cursor: pointer;
}
.textspan {
  color: red;
}
div.page {
  display: inline-block;
}
</style>
