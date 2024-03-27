<template>
  <div class="main container">
    <canvas ref="canvasElement" class="main__canvas" v-show="showCanvas"></canvas>
    <div class="main__tools">
      <input type="text" v-model="imageSrc" class="main__input" placeholder="Введите URL ссылку" >
      <button @click="loadImage(imageSrc)" ref="buttonClick" class="main__btn">Загрузить изображение</button>
      <input type="file" alt="Изображение" id="inputFile" @change="loadImageFromFile($event)" class="main__input-file">
      <label for="inputFile">Загрузить файл</label>
    </div>
    <div class="main__info" v-show="showCanvas">
      <p class="main__x">x - {{x}} px</p>
      <p class="main__y">y - {{y}} px</p>

      <div class="main__color" :style="{backgroundColor: rgbString}"></div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      showCanvas: false,
      showTools: true,
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
           this.showCanvas = true;
        })
        .catch(error => {
          console.error('Ошибка загрузки изображения:', error);
           this.showCanvas = false;
        });

    },
    loadImageFromFile (event) {
      const file = event.target.files[0];
      if (file && file.type.match('image.*')){
        // здесь тоже можно добавить showCanvas
        const reader = new FileReader();
        reader.onload = (e) => {
          this.drawCanvas(e.target.result)
          this.showCanvas = true;
        };
        reader.readAsDataURL(file);
      } else {
         this.showCanvas = false;
        console.error('Файл не является изображением')
      }
    },

    drawCanvas(imageUrl) {
      const canvasElement = this.$refs.canvasElement;
      const ctx = canvasElement.getContext('2d');
      const image = new Image(); // Создание нового объекта изображения
      image.src = imageUrl;
      // Установка источника изображения


      image.onload = () => { // функция для рендеренга изоб
        canvasElement.width = image.width;
        canvasElement.height = image.height;
        ctx.drawImage(image, 0, 0); // Вставка изображения в canvas
      };
    },
    handleCanvasClick(event) {
      const canvasElement = this.$refs.canvasElement
      const rect = canvasElement.getBoundingClientRect();
      // содержат координаты, где произошло событие
      const x = event.clientX - rect.left
      const y = event.clientY - rect.top
      const ctx = canvasElement.getContext('2d');
      const pixel = ctx.getImageData(x, y, 1, 1).data;
      // возращаем массив rgba
      const r = pixel[0]; // Красный
      const g = pixel[1]; // Зелёный
      const b = pixel[2]; // Синий

      this.rgbString = `rgb(${r}, ${g}, ${b})`
      this.x = Math.floor(x);
      this.y = Math.floor(y);
    },

  }
};
</script>

<style scoped lang="scss">
  .main {
    display: flex;
    flex-direction: column;
    align-items: center;

    &__info {
      position: absolute;
      top: 10px;
      left: 10px;
      display: flex;
      flex-direction: column;
      align-items: center;
    }
    p {
      color: #ffffff;
    }
    &__color {
      width: 1rem;
      height: 1rem;
    }
    &__tools {
      display: flex;
      align-items: center;
      flex-direction: column;
    }
    input[type="file"] {
      display: none;
    }

    label {
      font-family: "Poppins", sans-serif;
      display: inline-block;
      text-transform: uppercase;
      text-align: center;
      padding: 0.5rem 1rem;
      background: #c0392b;
      color: #ffffff;
      font-size: 1rem;
      letter-spacing: 1.5px;
      cursor: pointer;
      box-shadow: 5px 15px 25px rgba(0, 0, 0, 0.35);
      border-radius: 3px;
      margin-top: 10px;
    }

    label:hover {
      transform: scale(0.98);
      transition: all ease-in-out 0.07s;
    }

    &__btn {
      font-family: "Poppins", sans-serif;
      font-size: 1rem;
      text-transform: uppercase;
      text-align: center;
      padding: 0.5rem 1rem;
      background: #c0392b;
      color: #ffffff;
      letter-spacing: 1.5px;
      cursor: pointer;
      box-shadow: 5px 15px 25px rgba(0, 0, 0, 0.35);
      border-radius: 3px;
      border: none;
    }

    &__btn:hover {
      transform: scale(0.98);
      transition: all ease-in-out 0.07s;
    }

    &__input {
      width: 280px;
      height: 38px;
      border: #c0392b;
      border-radius: 3px;
      margin-bottom: 10px;
      margin-top: 10px;
    }
  }


</style>
