<template>
  <form class="form" @submit.prevent="onSubmit">
    <div class="form__personal-data">
      <CustomInput
        class="form__element"
        label="Фамилия"
        v-model="form.surname"
        :error="validationErrors.surname"
      />
      <CustomInput
        class="form__element"
        label="Имя"
        v-model="form.name"
        :error="validationErrors.name"
      />
      <CustomInput
        class="form__element"
        label="Отчество"
        v-model="form.paternal"
        :error="validationErrors.paternal"
      />
      <CustomInput
        class="form__element"
        label="Дата рождения"
        placeholder="дд.мм.гггг"
        type="datetime-local"
        v-model="form.dateOfBirth"
        mask="##.##.####"
        :error="validationErrors.dateOfBirth"
      />
    </div>
    <CustomInput
      class="form__element"
      label="E-mail"
      v-model="form.email"
      :error="validationErrors.email"
    />
    <CustomRadioGroup
      class="form__element"
      label="Пол"
      :options="genderOptions"
      v-model="form.gender"
    />
    <CustomSelect
      class="form__element"
      label="Гражданство"
      :options="citizenshipsOptions"
      optionValueName="nationality"
      optionLabelName="nationality"
      v-model="form.citizenship"
    />
    <div v-if="askLocalPassport" class="form__passport-data">
      <CustomInput
        class="form__element"
        label="Серия"
        v-model="form.serial"
        :error="validationErrors.serial"
      />
      <CustomInput
        class="form__element"
        label="Номер паспорта"
        v-model="form.number"
        :error="validationErrors.number"
      />
      <CustomInput
        class="form__element"
        label="Дата выдачи"
        placeholder="дд.мм.гггг"
        v-model="form.dateOfIssue"
        :error="validationErrors.dateOfIssue"
        mask="##.##.####"
      />
    </div>
    <div v-else class="form__passport-data">
      <CustomInput
        class="form__element"
        label="Фамилия на латинице"
        v-model="form.surnameLat"
        :error="validationErrors.surnameLat"
      />
      <CustomInput
        class="form__element"
        label="Имя на латинице"
        v-model="form.nameLat"
        :error="validationErrors.nameLat"
      />
      <CustomInput
        class="form__element"
        label="Номер паспорта"
        v-model="form.number"
      />
      <CustomSelect
        class="form__element"
        label="Страна выдачи"
        :options="citizenshipsOptions"
        optionValueName="nationality"
        optionLabelName="nationality"
        v-model="form.countryIssuer"
      />

      <CustomSelect
        class="form__element"
        label="Тип паспорта"
        :options="passportTypesOptions"
        optionValueName="type"
        optionLabelName="type"
        v-model="form.passportType"
      />
    </div>
    <CustomRadioGroup
      class="form__element"
      label="Меняли ли фамилию или имя?"
      :options="nameChangeOptions"
      v-model="form.changedName"
    />
    <div v-if="askPrevName" class="form__personal-data">
      <CustomInput
        class="form__element"
        label="Предыдущее имя"
        v-model="form.prevName"
        :error="validationErrors.prevName"
      />
      <CustomInput
        class="form__element"
        label="Предыдущая фамилия"
        v-model="form.prevSurname"
        :error="validationErrors.prevSurname"
      />
    </div>
    <button type="submit" class="form__submit-button">Submit</button>
  </form>
</template>

<script>
import CustomInput from "./CustomInput";
import CustomSelect from "./CustomSelect";
import CustomRadioGroup from "./CustomRadioGroup";

import citizenships from "../assets/data/citizenships.json";
import passportTypes from "../assets/data/passport-types.json";

const defaultErrors = {
  surname: "",
  name: "",
  paternal: "",
  dateOfBirth: "",
  email: "",
  gender: "",
  citizenship: "",
  serial: "",
  number: "",
  dateOfIssue: "",
  surnameLat: "",
  nameLat: "",
  countryIssuer: "",
  passportType: "",
  changedName: "",
  prevName: "",
  prevSurname: "",
};

export default {
  components: {
    CustomInput,
    CustomSelect,
    CustomRadioGroup,
  },
  data() {
    return {
      genderOptions: [
        {
          id: 1,
          value: "Мужской",
          label: "Мужской",
        },
        {
          id: 2,
          value: "Женский",
          label: "Женский",
        },
      ],
      citizenshipsOptions: citizenships,
      passportTypesOptions: passportTypes,
      nameChangeOptions: [
        {
          id: 3,
          value: "Да",
          label: "Да",
        },
        {
          id: 4,
          value: "Нет",
          label: "Нет",
        },
      ],
      form: {
        surname: "",
        name: "",
        paternal: "",
        dateOfBirth: "",
        email: "",
        gender: "Мужской",
        citizenship: "Russia",
        serial: "",
        number: "",
        dateOfIssue: "",
        surnameLat: "",
        nameLat: "",
        countryIssuer: "Russia",
        passportType: "Национальный паспорт",
        changedName: "Нет",
        prevName: "",
        prevSurname: "",
      },
      validationErrors: { ...defaultErrors },
    };
  },
  computed: {
    askLocalPassport() {
      return this.form.citizenship === "Russia";
    },
    askPrevName() {
      return this.form.changedName === "Да";
    },
  },
  methods: {
    checkDate(date) {
      const parts = date.split(".").map((d) => Number(d));
      if (parts.length < 3) return false;

      const dateObject = new Date(parts[2], parts[1] - 1, parts[0]);
      const isValid = dateObject && dateObject.getMonth() + 1 == parts[1];

      return isValid && dateObject < Date.now();
    },
    validateInputs() {
      this.validationErrors = { ...defaultErrors };

      const onlyRussianLetters = ["surname", "name", "paternal"];
      onlyRussianLetters.forEach((field) => {
        if (!/^[а-яА-Я]+$/.test(this.form[field])) {
          this.validationErrors[field] = "Введите только русские буквы";
        }
      });

      if (
        !/^[a-zA-Z0-9.!#$%&’*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$/.test(
          this.form.email
        )
      ) {
        this.validationErrors.email = "Введите корректный e-mail";
      }

      if (!this.checkDate(this.form.dateOfBirth)) {
        this.validationErrors.dateOfBirth = "Некорректная дата";
      }

      if (!this.askLocalPassport) {
        const onlyEnglishLetters = ["surnameLat", "nameLat"];
        onlyEnglishLetters.forEach((field) => {
          if (!/^[a-zA-Z]+$/.test(this.form[field])) {
            this.validationErrors[field] = "Введите только английские буквы";
          }
        });
      } else {
        if (!/^\d{4}$/.test(this.form.serial)) {
          this.validationErrors.serial = "Введите 4 цифры";
        }

        if (this.askLocalPassport && !/^\d{6}$/.test(this.form.number)) {
          this.validationErrors.number = "Введите 6 цифр";
        }

        if (!this.checkDate(this.form.dateOfIssue)) {
          this.validationErrors.dateOfIssue = "Некорректная дата";
        }
      }

      if (this.askPrevName) {
        const checkPrevName = ["prevName", "prevSurname"];
        checkPrevName.forEach((field) => {
          if (!/^[а-яА-я]+$/.test(this.form[field])) {
            this.validationErrors[field] = "Введите только русские буквы";
          }
        });
      }
    },
    onSubmit() {
      this.validateInputs();

      if (!Object.values(this.validationErrors).some((e) => Boolean(e))) {
        console.log(this.form);
      }
    },
  },
};
</script>

<style scoped>
.form {
  margin: 0 auto;
}

.form__element {
  margin: 1.5em 1em 0 0;
  width: 250px;
}

.form__personal-data {
  display: flex;
  flex-wrap: wrap;
}

.form__passport-data {
  display: flex;
  flex-wrap: wrap;
}

.form__submit-button {
  width: 150px;

  padding: 0.5em;
  margin: 2em 0;

  border: none;
  border-radius: 10px;

  background: #00bcd4;
}
</style>
