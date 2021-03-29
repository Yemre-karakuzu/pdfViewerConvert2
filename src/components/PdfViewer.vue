<template>
  <div id="pageContainer">
    <div class="pdfHeader">
      <div class="pdfHeaderinput">
        <button @click="currentPagePrev" class="button">
          <i class="fas fa-arrow-left"></i>
        </button>
        <span class="textspan"> {{ currentPage }} / {{ pageCount }} </span>
        <button @click="currentPageNext" class="button">
          <i class="fas fa-arrow-right"></i>
        </button>
        <button @click="zoomOut" class="button zoom">
          <i class="fas fa-search-minus"></i>
        </button>
        <button @click="zoomIn" class="button zoom">
          <i class="fas fa-search-plus"></i>
        </button>
      </div>
    </div>
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
      pageScale: 1.5,
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
      console.log("ss", currentPageNumber);
      this.pageCount = (await pdfto).numPages;
      loadingTask.promise.then((pdf) => {
        pdf.getPage(currentPageNumber).then((page) => {
          var scale = this.pageScale;
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
    zoomOut() {
      this.pageScale -= 1;
      this.getPdf();
    },
    zoomIn() {
      this.pageScale += 1;
      this.getPdf();
    },
  },
};
</script>

<style>
#pageContainer {
  margin: auto;
  width: 918px;
  height: 1188px;
  position: relative;
}
.pdfHeader {
  height: 140px;
  width: 50px;
  background: rgba(7, 20, 34, 0.4);
  position: absolute;
  right: 15px;
  top: 30%;
}
.pdfHeaderinput {
  display: flex;
  flex-direction: column;
  padding-top: 15px;
}
.pdfHeaderinput > * {
  margin-top: 5px;
}
.button {
  color: blue;
  background: white;
  cursor: pointer;
  width: 50px;
  height: 20px;
}
.textspan {
  color: red;
}
.zoom {
  font-size: 16px;
  background: transparent;
}
div.page {
  display: inline-block;
}
</style>
