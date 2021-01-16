<template>
  <div class="container-sm">
    <div class="card m-3 p-3">
      <h1 class="mb-3">Mantar Falan</h1>
      <div class="">
        <label for="formFile" class="form-label"
          >Başlamak için bir mantar fotoğrafı yükleyin.</label
        >
        <input class="form-control mb-2" type="file" @change="onFileChange" />
        <img v-if="url" :src="url" class="image mb-3" />
        <img v-show="false" id="currentImage" :src="url" class="mb-3" />
      </div>
      <div class="mb-4">
        <h6>Aşağıdaki mantar türlerinin tespiti yapılabilir.</h6>
        <div class="row">
          <div class="col">
            <ul class="list-group">
              <li class="list-group-item">Çayır Mantarı</li>
              <li class="list-group-item">Doru Renki Mantar</li>
            </ul>
          </div>
          <div class="col">
            <ul class="list-group">
              <li class="list-group-item">Kuzu Göbeği Mantarı</li>
              <li class="list-group-item">Tirmit Mantarı</li>
            </ul>
          </div>
        </div>
      </div>
      <div class="mb-3">
        <h6>Fotoğrafın hangi açıdan çekildiğini seçiniz.</h6>
        <div v-if="error" class="alert alert-danger" role="alert">
          Lütfen bir fotoğraf seçiniz.
        </div>
        <button class="btn btn-warning me-2" v-on:click="lookOut()">
          Gönder
        </button>
      </div>
      <div v-if="loading" class="spinner-border loading mb-2">
        <span class="visually-hidden">Loading...</span>
      </div>
      <div v-if="isDone">
        <div class="row mb-2">
          <h6>Sonuç</h6>
          <div class="col">
            <div class="card bg-success">
              <div class="card-body">
                <h5 class="card-title">
                  {{ response["outputs"]["Prediction"][0] }}
                </h5>
                <p class="card-text">
                  This is a wider card with supporting text below as a natural.
                </p>
              </div>
            </div>
          </div>
        </div>
        <div class="row">
          <h6>Değerlendirme</h6>
          <div class="col mb-3">
            <div class="card">
              <div class="card-body">
                <h5 class="card-title">
                  {{ response["outputs"]["Labels"][0][0] }}
                </h5>
                <p class="card-text">
                  This is a wider card with supporting text below as a natural.
                </p>
              </div>
              <div class="card-footer">
                <small class="text-muted"
                  >%{{
                    Number(response["outputs"]["Labels"][0][1]) * 100
                  }}</small
                >
              </div>
            </div>
          </div>
          <div class="col">
            <div class="card">
              <div class="card-body">
                <h5 class="card-title">
                  {{ response["outputs"]["Labels"][1][0] }}
                </h5>
                <p class="card-text">
                  This is a wider card with supporting text below as a natural.
                </p>
              </div>
              <div class="card-footer">
                <small class="text-muted"
                  >%{{
                    Number(response["outputs"]["Labels"][1][1]) * 100
                  }}</small
                >
              </div>
            </div>
          </div>
          <div class="col">
            <div class="card">
              <div class="card-body">
                <h5 class="card-title">
                  {{ response["outputs"]["Labels"][2][0] }}
                </h5>
                <p class="card-text">
                  This is a wider card with supporting text below as a natural.
                </p>
              </div>
              <div class="card-footer">
                <small class="text-muted"
                  >%{{
                    Number(response["outputs"]["Labels"][2][1]) * 100
                  }}</small
                >
              </div>
            </div>
          </div>
          <div class="col">
            <div class="card">
              <div class="card-body">
                <h5 class="card-title">
                  {{ response["outputs"]["Labels"][3][0] }}
                </h5>
                <p class="card-text">
                  This is a wider card with supporting text below as a natural.
                </p>
              </div>
              <div class="card-footer">
                <small class="text-muted"
                  >%{{
                    Number(response["outputs"]["Labels"][3][1]) * 100
                  }}</small
                >
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div>
      <p class="text-end footer">
        Tüm Hakları Saklıdır © 2020, Metin Yıldız 2020.
      </p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      file: null,
      url: null,
      error: false,
      loading: false,
      isDone: false,
      response: null,
    };
  },
  props: {},
  methods: {
    onFileChange(e) {
      this.file = e.target.files[0];
      this.url = URL.createObjectURL(this.file);
    },
    getBase64Image(img) {
      var canvas = document.createElement("canvas");
      canvas.width = img.width;
      canvas.height = img.height;
      var ctx = canvas.getContext("2d");
      ctx.drawImage(img, 0, 0);
      var dataURL = canvas.toDataURL("image/png");
      return dataURL.split(",")[1];
    },
    lookOut() {
      if (this.file === null) {
        this.error = true;
        return;
      }
      this.loading = true;
      this.isDone = false;
      this.error = false;

      const base64 = this.getBase64Image(
        document.getElementById("currentImage")
      );

      const requestOptions = {
        method: "POST",
        headers: {},
        body: JSON.stringify({
          inputs: {
            Image: base64,
          },
        }),
      };

      fetch(
        "http://localhost:38100/predict/12013e43-5112-44d2-98e7-16fa1df67041",
        requestOptions
      )
        .then((response) => response.json())
        .then((data) => {
          this.isDone = true;
          this.loading = false;
          this.response = data;
        });
    },
  },
};
</script>

<style scoped>
.image {
  height: 200px;
  border-radius: 5px;
}
.footer {
  font-size: 12px;
  font-weight: bold;
  margin-right: 20px;
}
.loading {
  align-self: center;
}
</style>
