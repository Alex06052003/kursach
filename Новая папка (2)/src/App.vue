<script>
export default {
  data() {
    return {
      input: ``, // Поле ввода
      output: ``, // Поле вывода
      isActive: false, // Нажата ли кнопка для переключения режма
      error: `` // Текст ошибки
    }
  },
  methods: {
    convert() {
      this.output = ``; // Очищаем поле вывода
      this.input = this.input.trim(); // Удаляем пробелы в начале и конце строки
      this.input = this.input.replace(/ +/g, ' '); // Удаляем лишние пробелы в середине строки

      if (this.input === ``) { // Введена пустая строка?
        this.error = `Ошибка: заполните поле ввода`; // Выводится сообщение об ошибке
        return;
      }

      // Проверяем, содержит ли строка русские символы
      let check = /[^абвгдеёжзийклмнопрстуфхцчшщъыьэюяАБВГДЕЁЖЗИЙКЛМНОПРСТУФХЦЧШЩЪЫЬЭЮЯ]/;
      if (!check.test(this.input)) {
        this.error = `Ошибка: кириллица не поддерживается. Используется кодировка ASCII (код символа 32-127). Буквы, цифры, знаки препинания и другие символы (англ.)`;
        return;
      }

      // Перебираем введенную строку
      for (let i = 0; i < this.input.length; i++) {
        let decimal = this.input[i].charCodeAt(0); //Каждый символ переводим в десятичное число кодировки Unicode

        //Проверка, правильно ли вводится двоичный код
        if (decimal < 32 || decimal > 127) {
          this.error = `Ошибка: кириллица не поддерживаются`;
          this.output = ``;
          return;
        }
        this.output += decimal.toString(2) + " "; // Переводим десятичные числа в двоичную систему
      }
      this.output = this.output.trim();
    },

    reconvert() {
      this.input = ``;
      this.output = this.output.trim();
      this.output = this.output.replace(/ +/g, ' ');

      if (this.output === ``) {
        this.error = `Ошибка: заполните поле ввода`;
        return;
      }

      // Проверяем, содержит ли строка только символы "0" и "1" и " "
      let check = /[^01 ]/;
      if (check.test(this.output)) {
        this.error = `Ошибка: введите только 0, 1, разделяя пробелом`;
        return;
      }

      // Разделение строки на отдельные двоичные числа
      let binaryArray = this.output.split(" ");

      // Преобразование каждого двоичного числа в десятичное число и получение символа
      let text = "";
      for (let i = 0; i < binaryArray.length; i++) {
        let binary = binaryArray[i];
        let decimal = parseInt(binary, 2);

        //Проверка, правильно ли вводится двоичный код
        if (decimal < 32 || decimal > 127) {
          this.error = `Ошибка: данный двоичный код не поддерживается`;
          this.input = ``; // Очищаем поле ввода
          return;
        }
        text += String.fromCharCode(decimal); // Получение символа
      }
      this.input = text;
    },

    reverse() {
      this.isActive = !this.isActive; // Меняем режим работы транслятора
    },

    clearError() {
      this.error = ``; // Очищаем сообщение об ошибке
    }
  }
}
</script>

<template>
  <div class="container">
    <div class="d-flex main-field">
      <div>
        <h2 v-if="!isActive">Ввод ЯП</h2>
        <h2 v-if="isActive">Вывод ЯП</h2>
        <div class="form-floating">
          <textarea class="form-control" placeholder="Leave a comment here" style="width: 400px;  height: 400px;"
            id="floatingTextarea1" v-model="input" :disabled="isActive"></textarea>
          <label for="floatingTextarea2" v-if="!isActive">INPUT</label>
          <label for="floatingTextarea2" v-if="isActive">OUTPUT</label>
        </div>
      </div>
      <img src="./assets/next.png" alt="arrow" :class="[{ reverse: isActive }, 'image']" @click="reverse">
      <div>
        <h2 v-if="!isActive">Вывод бинарный код</h2>
        <h2 v-if="isActive">Ввод в бинарном коде</h2>
        <div class="form-floating">
          <textarea class="form-control" placeholder="Leave a comment here" style="width: 400px;  height: 400px;"
            id="floatingTextarea2" v-model="output" :disabled="!isActive"></textarea>
          <label for="floatingTextarea2" v-if="!isActive">OUTPUT</label>
          <label for="floatingTextarea2" v-if="isActive">INPUT</label>
        </div>
      </div>
    </div>
    <div class="d-flex btn-field">
      <button type="button" class="btn btn-primary" @click="convert" v-if="!isActive">Convert</button>
      <button type="button" class="btn btn-danger" @click="reconvert" v-if="isActive">Reconvert</button>
    </div>
    <div class="alert alert-danger alert-dismissible fade show error-message" role="alert" v-if="error">
      <strong>!ERROR!</strong> {{ error }}
      <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close" @click="clearError"></button>
    </div>
  </div>
</template>

<style>
* {
  margin: 0;
  padding: 0;
}

.main-field {
  margin: 30px;
  justify-content: center;
  align-items: center;
  gap: 40px;
}

.image {
  width: 50px;
  transition: all .4s;
}

.image:hover {
  scale: 1.2;
}

.btn-field {
  margin-top: 50px;
  justify-content: center;
}

.reverse {
  transform: rotate(180deg);
}

.error-message {
  margin-top: 10px;
}
</style>
