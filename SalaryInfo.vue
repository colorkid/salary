<template>
  <div class="salary-block">
    <h1>{{ msg }}</h1>
    <div class="form-group">
      <label for="language">Выбирите специальность или язык/фреймвок</label>
      <select class="form-control" id="language">
        <option v-for="item in position" v-bind:value="item.item">
          {{ item.item }}
        </option>
      </select>
    </div>
    <div class="form-group">
      <label class="salary-block__label">Выбирите уровень</label>
      <div class="form-check form-check-inline" v-for="item in level">
        <input class="form-check-input experience-checkbox" type="radio" name="experience" v-bind:id="item.item" v-bind:value="item.years">
        <label class="form-check-label" v-bind:for="item.item">{{ item.item }}</label>
      </div>
    </div>
    <div class="form-group">
      <label for="language">Выбирите город</label>
      <select class="form-control" id="city">
        <option v-for="item in city" v-bind:value="item.id">
          {{ item.item }}
        </option>
      </select>
    </div>
    <transition name="fade">
      <div class="answer alert alert-primary" role="alert" v-if="answer">
        {{ answer }}
      </div>
    </transition>
    <button type="button" class="btn btn-primary" v-on:click="toMeasure">Измерить</button>
  </div>
</template>

<script>
export default {
  name: 'SalaryInfo',
  data () {
    return {
      msg: 'Зарплатомер',
      position: [
        {item: 'Front-End Developer'},
        {item: 'Back-End Developer'},
        {item: 'Full-Stack Developer'},
        {item: 'Html Верстальщик'},
        {item: 'Php программист'},
        {item: 'JavaScript программист'},
        {item: 'Java программист'},
        {item: 'Python программист'},
        {item: 'Битрикс программист'},
        {item: '1С программист'},
        {item: 'JavaScript'},
        {item: 'Php'},
        {item: 'Java'},
        {item: 'Python'},
        {item: 'React'},
        {item: 'Angular'},
        {item: 'Vue'},
        {item: 'Laravel'},
        {item: 'Yii 2'},
        {item: 'Dev Ops'},
        {item: 'Системный администратор'}
      ],
      level: [
        {
          item: 'entry',
          years: 'noExperience'
        },
        {
          item: 'junior',
          years: 'between1And3'
        },
        {
          item: 'middle',
          years: 'between3And6'
        },
        {
          item: 'senior',
          years: 'moreThan6'
        }
      ],
      city: [
        {
          item: 'Москва',
          id: 1
        },
        {
          item: 'Санкт-Петербург',
          id: 2
        },
        {
          item: 'Казань',
          id: 88
        },
        {
          item: 'Ростов-На-Дону',
          id: 76
        },
        {
          item: 'Екатеринбург',
          id: 3
        },
        {
          item: 'Новосибирск',
          id: 4
        },
        {
          item: 'Томск',
          id: 90
        },
        {
          item: 'Самара',
          id: 78
        }
      ],
      answer: "",
      dataAnswer: []
    }
  },

  mounted: function () {
    $(".experience-checkbox").first().attr("checked", true);
  },

  methods: {
    toMeasure: function () {
      let selectedPositionValue = document.querySelector("#language").value;
      let selectedExperienceValue = $(".experience-checkbox:checked").val();
      let selectedCityValue = document.querySelector("#city").value;

      $.getJSON(`https://api.hh.ru/vacancies?text= ${selectedPositionValue} &only_with_salary=true&area= ${selectedCityValue} &per_page=100&experience= ${selectedExperienceValue}`, (data) =>  {
        this.dataAnswer = data.items.slice();
        this.answer = `Данныйх нет. Измените параметры.`;
        this.payrollPreparation;
      });
    }
  },

  computed: {
    payrollPreparation: function () {
      
      let itemsPriceArr = [];

      for (let i = 0; i < this.dataAnswer.length; i++) {
          let salaryObj = this.dataAnswer[i].salary;
          
          if (salaryObj.currency !== "RUR") continue;
          
          let fromSalary = salaryObj.from;
          let toSalary = salaryObj.to;

          itemsPriceArr.push((fromSalary + toSalary) / 2);
      }

      if (itemsPriceArr.length === 0) return;

      let resultAllSalary = itemsPriceArr.reduce(function(sum, current) {
        return sum + current
      });

      const round = (n, decimals = 0) => Number(`${Math.round(`${n}e${decimals}`)}e-${decimals}`);

      this.answer = round(resultAllSalary / itemsPriceArr.length, 2) + ` rub.`;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .salary-block {
    width: 100%;
    max-width: 480px;
    margin: 0 auto;
  }

  .form-group {
    margin-top: 40px;
    padding: 15px;
    border-radius: 4px;
    box-shadow: 0 1px 4px 1px #d2d2d2;
  }

  .salary-block__label {
    width: 100%;
  }

  .btn {
    margin-top: 20px;
  }

  .fade-enter-active, .fade-leave-active {
    transition: opacity .5s;
  }

  .fade-enter, .fade-leave-to /* .fade-leave-active до версии 2.1.8 */ {
    opacity: 0;
  }
</style>
