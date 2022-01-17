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

      <div class="search-result">
        <div class="gcse-searchresults"></div>
      </div>
    </div>
    <el-button type="primary" @click="onClickAdd">Thêm</el-button>
    <img :width="250" :src="selectedImage" />

    <el-dialog
      title="Tìm kiếm ảnh"
      :visible.sync="dialogVisible"
      width="80%"
      @open="onInitial"
      @opened="addOpened"
      @closed="onEndSearch"
      append-to-body
    >
      <!-- <div class="gcse-searchresults-only"></div> -->
      <div class="search-box" ref="search"></div>
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
      dialogVisible: false,
      isDisabled: true,
    };
  },

  created() {
    if (window.google === undefined || !window.google.search) {
      loadScript("https://cse.google.com/cse.js?cx=e7b5ff1a9b019e32b");
    }
  },

  methods: {
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
      var searchBox = document.getElementById("___gcse_0");
      var searchResult = document.getElementById("___gcse_1");
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
      const response = await fetch(
        document
          .getElementsByClassName("gs-selectedImageResult")[0]
          .getElementsByClassName("gs-imagePreviewArea")[0]
          .getElementsByClassName("gs-previewLink")[0]
          .getElementsByTagName("img")[0]
          .getAttribute("src")
      );
      console.log(response);
      const data = await response.blob();
      console.log(data);

      const metadata = { type: "image/png" };
      const file = new File([data], "test.png", metadata);
      console.log(file);
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
  /* display: none; */
}
</style>
