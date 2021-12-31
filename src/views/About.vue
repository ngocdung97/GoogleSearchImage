<template>
  <div
    class="gcse-search"
    data-newWindow="true"
    data-imageSearchLayout="popup"
    data-enableImageSearch="true"
    data-defaultToImageSearch="true"
  ></div>
</template>
<script>
export default {
  mounted() {
    debugger;
    let recaptchaScript = document.createElement("script");
    recaptchaScript.setAttribute(
      "src",
      "https://cse.google.com/cse.js?cx=e7b5ff1a9b019e32b"
    );
    document.head.appendChild(recaptchaScript);
  },
};
</script>
<style lang="scss" scoped></style>
