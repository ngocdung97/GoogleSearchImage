<template>
  <div class="container">
    <!-- <div class="group-search">
      <el-input
        placeholder="Tìm kiếm google search"
        v-model="searchText"
      ></el-input>
    </div> -->
    <div class="google-search">
      <div
        class="gcse-searchbox"
        data-newWindow="true"
        data-imageSearchLayout="popup"
        data-enableImageSearch="true"
        data-defaultToImageSearch="true"
      ></div>

      <div id="search-result" class="search-result">
        <div class="gcse-searchresults"></div>
      </div>
    </div>
    <el-button type="primary" @click="onClickAdd">Thêm</el-button>
    <img :width="250" :src="selectedImage" />

    <el-dialog
      title="Tìm kiếm ảnh"
      :visible.sync="dialogVisible"
      @open="onInitial"
      @opened="addOpened"
      @closed="onEndSearch"
      append-to-body
    >
      <!-- <div class="gcse-searchresults-only"></div> -->
      <div class="search-box" ref="search">
        <!-- <div class="g-image-preview" v-show="selectedImage">
          <img :src="selectedImage" alt="" />
        </div> -->
      </div>
      <div id="upload" class="upload"></div>
      <!-- <div
        class="gcse-search"
        data-newWindow="true"
        data-imageSearchLayout="popup"
        data-enableImageSearch="true"
        data-defaultToImageSearch="true"
      ></div> -->
      <!-- <div id="test"></div> -->
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">Cancel</el-button>
        <el-button type="primary" @click="onChoose" :disabled="isDisabled"
          >Confirm</el-button
        >
      </span>
    </el-dialog>
    <!-- <div class="gcse-searchresults"></div> -->
  </div>
</template>
<script>
import loadScript from "load-script";
// import { Button } from 'components';
export default {
  data() {
    return {
      isSearch: false,
      selectedImage: "",
      showPreview: false,
      dialogVisible: false,
      isDisabled: true,
      iconClose: require("../assets/icon-close.svg"),
    };
  },

  created() {
    if (window.google === undefined || !window.google.search) {
      loadScript("https://cse.google.com/cse.js?cx=e7b5ff1a9b019e32b");
    }

    window.__gcse || (window.__gcse = {});
    window.__gcse.searchCallbacks = {
      web: {
        rendered: function () {
          debugger;
          document.getElementsByClassName("gsc-cursor")[0].style.fontSize =
            "20px";
        },
      },
    };
  },

  mounted() {
    window.__gcse || (window.__gcse = {});
    window.__gcse.searchCallbacks = {
      image: {
        rendered: this.myImageResultsRenderedCallback,
      },
    };
  },

  methods: {
    myImageResultsRenderedCallback(name, q, promos, results) {
      const me = this;
      for (const div of promos.concat(results)) {
        div.addEventListener("click", function (e) {
          var list = document.getElementById("search-result");
          const innerDiv = document.createElement("div");
          innerDiv.setAttribute("id", "g-image-preview-bndung");

          me.addClassToElement(
            innerDiv,
            "relative flex justify-between items-center"
          );
          const image = document.createElement("img");
          debugger;
          me.addClassToElement(
            image,
            "google-image-preview h-full p-3 bg-neutral-500"
          );
          image.src =
            e.currentTarget.children[1].getElementsByTagName("img")[0].src;
          // checkBox.type = "checkbox";
          // checkBox.name = "save";
          innerDiv.appendChild(image);

          // thêm icon hủy
          const iconClose = document.createElement("img");
          iconClose.src = require("../assets/icon-close.svg");
          iconClose.addEventListener("click", function (e) {
            console.log(e);
            debugger;
            me.modifyGrid(false);
            me.removePreview();
          });
          me.addClassToElement(
            iconClose,
            "absolute top-6 right-6 h-6 w-6 bg-gray-500 cursor-pointer"
          );
          // iconClose.classList.add("absolute t-6 r-6 ");
          innerDiv.appendChild(iconClose);
          debugger;
          if (document.getElementById("g-image-preview")) {
            list.removeChild(document.getElementById("g-image-preview"));
          }
          list.appendChild(innerDiv);

          // thu nhỏ grid
          me.modifyGrid(true);
        });
      }
    },

    closePreview() {
      const me = this;

      me.modifyGrid(false);
      me.removePreview();
    },

    removePreview() {
      var list = document.getElementById("search-result");

      if (document.getElementById("g-image-preview")) {
        list.removeChild(document.getElementById("g-image-preview"));
      }
    },

    addClassToElement(element, className) {
      className.split(" ").map((item) => {
        element.classList.add(item);
      });
    },

    modifyGrid(hasPreview) {
      if (hasPreview)
        document
          ?.getElementsByClassName("gsc-expansionArea")[1]
          ?.setAttribute("style", "grid-template-columns: 1fr 1fr");
      else
        document
          ?.getElementsByClassName("gsc-expansionArea")[1]
          ?.setAttribute("style", "grid-template-columns: 1fr 1fr 1fr 1fr");
    },

    onClickAdd() {
      this.dialogVisible = true;

      var button = document.getElementsByClassName("gsc-search-button-v2")[0];
      if (button) button.addEventListener("click", this.turnOnSearch);
    },

    onEndSearch() {
      var button = document.getElementsByClassName("gsc-search-button-v2")[0];

      button.removeEventListener("click", this.turnOnSearch);
    },

    turnOnSearch() {
      console.log("event");

      this.isDisabled = false;
      this.isSearch = true;
    },

    onInitial() {
      this.onSearch(String.empty);
    },

    onSearch(query) {
      var inputSearch = document.getElementById("gs_tti50");
      if (inputSearch)
        inputSearch.getElementsByTagName("input")[0].value = query || "";

      var button = document.getElementsByClassName(
        "gsc-search-button gsc-search-button-v2"
      )[0];

      if (button) {
        button.click();
      }
    },

    addOpened() {
      console.log("opened");
      // gán place holder

      this.modifyGrid(false);
      const input = document
        ?.getElementById("gs_tti50")
        ?.getElementsByTagName("input")[0];
      debugger;

      if (document?.getElementById("g-image-preview-bndung"))
        this.addClassToElement(
          document?.getElementById("g-image-preview-bndung"),
          "hidden"
        );

      debugger;
      // this.selectedImage = "";

      // document
      //   ?.getElementsByClassName("gs-image")
      //   ?.addEventListener("click", this.onPreviewImage);

      input?.setAttribute("placeholder", "Enter your number");

      var searchBox = document.getElementById("___gcse_0");
      var searchResult = document.getElementById("search-result");
      this.$refs?.search.appendChild(searchBox);
      this.$refs?.search.appendChild(searchResult);
      this.isSearch = false;
    },

    displaySearchResult(isSearch) {
      var searchResultForm = document.getElementById("___gcse_1");
      var uploadForm = document.getElementById("upload");

      if (!isSearch) {
        uploadForm.style.display = "block";
        searchResultForm.style.display = "none";
      } else {
        uploadForm.style.display = "none";
        searchResultForm.style.display = "block";
      }
    },

    async onChoose() {
      this.selectedImage = document
        .getElementsByClassName("gs-selectedImageResult")[0]
        .getElementsByClassName("gs-imagePreviewArea")[0]
        .getElementsByClassName("gs-previewLink")[0]
        .getElementsByTagName("img")[0]
        .getAttribute("src");

      this.dialogVisible = false;
      // const response = await fetch(
      //   document
      //     .getElementsByClassName("gs-selectedImageResult")[0]
      //     .getElementsByClassName("gs-imagePreviewArea")[0]
      //     .getElementsByClassName("gs-previewLink")[0]
      //     .getElementsByTagName("img")[0]
      //     .getAttribute("src")
      // );
      // console.log(response);
      // const data = await response.blob();
      // console.log(data);

      // const metadata = { type: "image/png" };
      // const file = new File([data], "test.png", metadata);
      // console.log(file);
    },
  },

  watch: {
    isSearch(val) {
      this.displaySearchResult(val);
    },
  },
};
</script>
<style lang="scss">
.___gcse_1 {
  width: 100%;
}

.google-image-preview {
  width: 800px;
  height: 521px;
  object-fit: cover;
}

.gs-selectedImageResult {
  height: auto !important;
}

.gs-imagePreviewArea {
  display: none !important;
}

.el-dialog {
  width: 1000px !important;
}

.gsc-expansionArea {
  display: grid !important;
  grid-template-columns: 1fr 1fr 1fr 1fr;
  gap: 5px !important;
}

.gs-image {
  width: 100% !important;
  height: 190px !important;
  object-fit: cover !important;
}

.gsc-imageResult {
  overflow: hidden;
}

.gsc-positioningWrapper,
.gsc-above-wrapper-area,
.gs-previewSnippet {
  display: none;
}

.upload {
  background-color: red;
  width: 100%;
  height: 500px;
}

.gsc-control-cse {
  height: 500px;
  margin: 12px 0;
  overflow-y: scroll;
}

.gsc-search-button-v2 {
  height: 35px;
}

.gsc-wrapper {
  margin-top: 12px;
}

.gs-image-popup-box {
  display: none !important;
}

.gs-previewLink {
  pointer-events: none;
}
</style>

<style style="scss" scoped>
.container {
  height: calc(100% - 84px);
  width: 100%;
  overflow-y: scroll;
}

.google-search {
  display: none;
}

.search-result {
  display: flex;
}

.google-image-preview {
  width: 459px;
  height: 100%;
  padding: 12px;
  object-fit: cover;
}
</style>
