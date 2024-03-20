<template>
  <div class="main__container">
    <canvas ref="canvasElement"></canvas>
    <input type="text" v-model="imageSrc">
    <button @click="loadImage(imageSrc)">Загрузить изображение</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      imageSrc: '', // Ссылка на изображение, вводимая пользователем
    };
  },
  methods: {
    loadImage(imageSrc) {
      const imageUrl = imageSrc;
      // Использую fetch API для запроса изображения
      fetch(imageUrl)
        .then(response => {
          if (!response.ok) {
            throw new Error('Сетевая ошибка при загрузке изображения');
          }
          return response.blob(); // Преобразование ответа в Blob
        })
        .then(blob => {
          const imageUrl = URL.createObjectURL(blob); // Создание временного URL для изображения
          this.drawCanvas(imageUrl); // Отрисовка изображения на canvas
        })
        .catch(error => console.error('Ошибка загрузки изображения:', error));
    },
    drawCanvas(imageUrl) {
      const canvasElement = this.$refs.canvasElement;
      const ctx = canvasElement.getContext('2d');
      const image = new Image(); // Создание нового объекта изображения
      image.src = imageUrl;
      // Установка источника изображения
      image.onload = () => { // функция для рендеренга изоб
        canvasElement.width = image.width; // Установка ширины canvas равной ширине изображения
        canvasElement.height = image.height; // Установка высоты canvas равной высоте изображения
        ctx.drawImage(image, 0, 0); // Вставка изображения в canvas
      };
    }
  }
};
</script>

<style scoped lang="scss">
/* Ваши стили */
</style>
