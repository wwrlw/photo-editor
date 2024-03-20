<script>


export default {
  data() {
    return {
      imageSrc: '', // Ссылка на изображение
    };
  },
  methods: {
    loadImage(imageSrc) {
      // URL изображения для загрузки
      let imageUrl = imageSrc;

      // Использую fetch API для запроса изображения
      fetch(imageUrl)
        .then(response => {
          if (!response.ok) {
            throw new Error('Сетевая ошибка при загрузке изображения');
          }
          return response.blob(); // Преобразование ответа в Blob
        })
        .then(blob => {
          this.imageSrc = URL.createObjectURL(blob); // Создание и установка URL из Blob
        })
        .catch(error => console.error('Ошибка загрузки изображения:', error));
    }
  }
};

</script>

<template>
  <div class="main__container">
    <canvas id="canvas" class="canvas" width="150px" height="150px"></canvas>
    <p class="main__x">x: {{x}}</p>
    <p class="main__x">x: {{y}}</p>
    <input type="text" v-model="imageSrc">
<!--    <button @click="loadImage">Загрузить изображение</button>-->
    <img v-if="imageSrc" :src="imageSrc" alt="Загруженное изображение" />
  </div>
</template>

<style scoped lang="scss">


</style>
