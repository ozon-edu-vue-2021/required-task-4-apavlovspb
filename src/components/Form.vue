<template>
  <div class="form">
    <div class="row">
      <label for="surname"> Фамилия </label>
      <input v-model="formData.surname" id="surname" placeholder="" />
    </div>
    <div class="row">
      <label for="name"> Имя </label>
      <input v-model="formData.name" id="name" placeholder="" />
    </div>
    <div class="row">
      <label for="middlename"> Отчество </label>
      <input v-model="formData.middlename" id="middlename" placeholder="" />
    </div>
    <div class="row">
      <label for="birthdate"> Дата Рождения </label>
      <input
        v-model="formData.birthdate"
        id="birthdate"
        placeholder=""
        type="date"
      />
    </div>
    <div class="row">
      <label for="email"> E-mail </label>
      <input v-model="formData.email" id="email" placeholder="" />
    </div>
    <div class="row radio-row">
      <div for="sex">Пол</div>
      <input
        v-model="formData.sex"
        id="male"
        placeholder=""
        type="radio"
        value="male"
      />
      <label for="male">Man</label>
      <input
        v-model="formData.sex"
        id="female"
        placeholder=""
        type="radio"
        value="female"
      />
      <label for="female">Woman</label>
    </div>

    <div
      class="row citizenship-selector"
      v-click-outside="closeCitizenDropdown"
    >
      <label for="citizenships">Гражданство</label>
      <input
        id="citizenships"
        @focus="isCitizenshipOpen = true"
        v-model="formData.citizenship"
      />
      <div v-if="isCitizenshipOpen">
        <ul v-if="availableСitizenships.length">
          <li
            v-for="citizenship in filtredCitizenships"
            :key="citizenship.uid"
            @click="onCitizenClick(citizenship)"
          >
            {{ citizenship.language }}
          </li>
        </ul>
        <div v-else>Ничего не найдено</div>
      </div>
    </div>

    <div
      v-if="formData.citizenship && formData.citizenship === 'Russian'"
      class="passport russian"
    >
      <div class="row">
        <label for="ruPassportSeries"> Серия паспорта </label>
        <input
          v-model="formData.passportDetails.series"
          id="ruPassportSeries"
          placeholder=""
        />
      </div>
      <div class="row">
        <label for="ruPassportNumber"> Номер паспорта </label>
        <input
          v-model="formData.passportDetails.number"
          id="ruPassportNumber"
          placeholder=""
        />
      </div>
      <div class="row">
        <label for="ruPassportIssueDate"> Дата выдачи </label>
        <input
          v-model="formData.passportDetails.issueDate"
          type="date"
          id="ruPassportIssueDate"
          placeholder=""
        />
      </div>
    </div>
    <div v-else-if="formData.citizenship" class="passport not_russian">
      <div class="row">
        <label for="passportSurname"> Фамилия на латинице </label>
        <input
          v-model="formData.passportDetails.passportSurname"
          id="passportSurname"
          placeholder=""
        />
      </div>
      <div class="row">
        <label for="passportName"> Имя на латинице </label>
        <input
          v-model="formData.passportDetails.passportName"
          id="passportName"
          placeholder=""
        />
      </div>
      <div class="row">
        <label for="passportNumber"> Номер паспорта </label>
        <input
          v-model="formData.passportDetails.number"
          id="passportNumber"
          placeholder=""
        />
      </div>
      <div
        class="row citizenship-selector"
        v-click-outside="closeCountryDropdown"
      >
        <label for="passportCountry">Страна выдачи</label>
        <input
          id="passportCountry"
          @focus="isCountryOpen = true"
          v-model="formData.passportDetails.country"
        />
        <div v-if="isCountryOpen">
          <ul v-if="availableСitizenships.length">
            <li
              v-for="citizenship in filtredCountry"
              :key="citizenship.uid"
              @click="onCountryClick(citizenship)"
            >
              {{ citizenship.nationality }}
            </li>
          </ul>
          <div v-else>Ничего не найдено</div>
        </div>
      </div>
      <div
        class="row citizenship-selector"
        v-click-outside="closePassportTypeDropdown"
      >
        <label for="passportType"> Тип паспорта </label>
        <input
          id="passportType"
          @focus="isPassportTypeOpen = true"
          v-model="formData.passportDetails.passportType"
        />
        <div v-if="isPassportTypeOpen">
          <ul v-if="availablePassportTypes.length">
            <li
              v-for="passportType in availablePassportTypes"
              :key="passportType.id"
              @click="onPassportClick(passportType)"
            >
              {{ passportType.type }}
            </li>
          </ul>
          <div v-else>Ничего не найдено</div>
        </div>
      </div>
    </div>
    <div class="row radio-row">
      <div>Меняли ли фамилию?</div>
      <input
        v-model="formData.changedSomebody"
        id="no"
        placeholder=""
        type="radio"
        value="false"
      />
      <label for="no">Нет</label>
      <input
        v-model="formData.changedSomebody"
        id="yes"
        placeholder=""
        type="radio"
        value="true"
      />
      <label for="yes">Да</label>
    </div>
    <template v-if="formData.changedSomebody === 'true'">
      <div class="row">
        <label for="passportNumber"> Предыдущая Фамилия </label>
        <input
          v-model="formData.prevSurname"
          id="passportNumber"
          placeholder=""
        />
      </div>
      <div class="row">
        <label for="passportNumber"> Предыдущая Имя </label>
        <input
          v-model="formData.prevName"
          id="passportNumber"
          placeholder=""
        /></div
    ></template>

    <button @click="onSubmit">Отправить</button>
  </div>
</template>

<script>
import ClickOutside from "vue-click-outside";
import { format } from "date-fns";
import citizenships from "../../src/assets/data/citizenships.json";
import passportTypes from "../../src/assets/data/passport-types.json";

export default {
  directives: {
    ClickOutside,
  },
  computed: {
    filtredCitizenships() {
      return this.availableСitizenships.filter(
        (item) => item.language !== this.formData.citizenship
      );
    },
    filtredCountry() {
      return this.availableСitizenships.filter(
        (item) => item.nationality !== this.formData.passportDetails.country
      );
    },
  },
  data() {
    return {
      formData: {
        surname: "",
        name: "",
        middlename: "",
        birthdate: format(new Date(0), "yyyy-MM-dd"),
        email: "",
        sex: "female",
        citizenship: "",
        changedSomebody: "false",
        prevName: "",
        prevSurname: "",
        passportDetails: {
          series: "",
          number: "",
          issueDate: format(new Date(0), "yyyy-MM-dd"),
          passportName: "",
          passportSurname: "",
          country: "",
          passportType: "",
        },
      },
      isCitizenshipOpen: false,
      isCountryOpen: false,
      isPassportTypeOpen: false,
      availableСitizenships: citizenships,
      availablePassportTypes: passportTypes,
    };
  },
  components: {
    // InputForm,
  },
  methods: {
    onSubmit() {
      console.log(JSON.stringify(this.formData));
    },
    closeCitizenDropdown() {
      this.isCitizenshipOpen = false;
    },
    closeCountryDropdown() {
      this.isCountryOpen = false;
    },
    closePassportTypeDropdown() {
      this.isPassportTypeOpen = false;
    },
    onCitizenClick(selectedCitizen) {
      this.formData.citizenship = selectedCitizen.language;
      this.formData.passportDetails = {
        series: "",
        number: "",
        issueDate: format(new Date(0), "yyyy-MM-dd"),
        passportName: "",
        passportSurname: "",
        country: "",
        passportType: "",
      };
      this.closeCitizenDropdown();
    },
    onCountryClick(country) {
      this.formData.passportDetails.country = country.nationality;
      this.closeCountryDropdown();
    },
    onPassportClick(passport) {
      this.formData.passportDetails.passportType = passport.type;
      this.closePassportTypeDropdown();
    },
  },
};
</script>

<style scoped>
.form {
  display: grid;
  grid-template-rows: 1fr;
  grid-gap: 12px;
  width: 300px;
}
.passport {
  display: grid;
  grid-template-rows: 1fr;
  grid-gap: 12px;
}
.row {
  display: grid;
  grid-template-columns: 1fr;
  grid-gap: 6px;
  width: 300px;
}
.radio-row {
  display: grid;
  grid-template-columns: auto auto auto auto;
  grid-column-gap: 0;
}
.radio-row div {
  grid-column: 1/5;
}
.radio-row label {
  height: 100%;
  display: flex;
  justify-content: flex-start;
  align-items: center;
}
.radio-row * {
  align-self: center;
}
input {
  outline: none;
  height: 34px;
  width: 100%;
}
</style>
