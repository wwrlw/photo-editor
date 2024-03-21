<template>
  <div class="main container">
    <canvas ref="canvasElement" class="main__canvas"></canvas>
    <input type="text" v-model="imageSrc" class="main__input">
    <button @click="loadImage(imageSrc)" class="main__btn">Загрузить изображение</button>
    <p class="main__x">x - {{x}}</p>
    <p class="main__y">y - {{y}}</p>

    <div class="main__color" :style="{backgroundColor: rgbString}"></div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      imageSrc: '', // Ссылка на изображение, вводимая пользователем
      x: this.x,
      y: this.y,
      rgbString: 'rgb(255, 255, 255)'
    };
  },
  mounted() {
    this.$refs.canvasElement.addEventListener('mousemove', this.handleCanvasClick) // Добавляем обработчик события при наведении мыши
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
      const RenderImg = () => {
        // image.onload ...
      }

      image.onload = () => { // функция для рендеренга изоб
        canvasElement.width = image.width;
        canvasElement.height = image.height;
        ctx.drawImage(image, 0, 0); // Вставка изображения в canvas
      };
    },
    handleCanvasClick(event) {
      const canvasElement = this.$refs.canvasElement
      const rect = canvasElement.getBoundingClientRect();
      const x = event.clientX - rect.left
      const y = event.clientY - rect.top
      const ctx = canvasElement.getContext('2d');
      const pixel = ctx.getImageData(x, y, 1, 1).data;

      const r = pixel[0]; // Красный
      const g = pixel[1]; // Зелёный
      const b = pixel[2]; // Синий

      this.rgbString = `rgb(${r}, ${g}, ${b})`
      this.x = x;
      this.y = y;
    },
    removeEvent() {
      this.$refs.canvasElement.removeEventListener('mousemove', this.handleCanvasClick)
    }
  }
};
</script>

<style scoped lang="scss">
  .main {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;

    &__canvas {
      margin-top: 1rem;
      margin-bottom: 1rem;
    }
    &__input {
      padding: 0.5rem 1rem 0.5rem 1rem;
      margin-bottom: 1rem;
      border-radius: 5px;
      border: solid 1px green;
    }
    &__input:hover {

    }
    &__btn {
      padding: 0.5rem 1rem 0.5rem 1rem;
      border-radius: 5px;
      border: solid 1px green;
      background-color: #fff;
    }

    &__color {
      width: 10px;
      height: 10px;
    }
    p {
      color: #ffffff;
    }
  }


</style>
