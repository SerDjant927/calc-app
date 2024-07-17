<template>
    <section class="calculator-section">
        <div class="calculator-container">
            <h1 class="calculator-title">Тарифный калькулятор</h1>
            <label for="tariff">Выберите тариф:</label>
            <select v-model="selectedTariff" @change="calculateTotal">
                <option value="standard">Стандартный</option>
                <option value="advanced">Продвинутый</option>
            </select>

            <label for="currency">Выберите валюту:</label>
            <select v-model="selectedCurrency" @change="calculateTotal">
                <option value="CNY">Китайский юань</option>
                <option value="KZT">Казахстанский тенге</option>
                <option value="RUS">Российский рубль</option>
            </select>

            <label for="paymentPeriod">Выберите период оплаты:</label>
            <select v-model="selectedPeriod" @change="calculateTotal">
                <option value="month">За месяц</option>
                <option value="year">За год</option>
            </select>

            <p>Сумма для оплаты: {{ total }}</p>
            <p v-if="discount">Выгоднее на: {{ totalWithDiscount }}</p>
        </div>
    </section>
</template>

<script>
import axios from 'axios';
export default {
    name: 'MainContent',
    data() {
        return {
            tariffs: {
                standard: {
                    month: 100,
                    year: 1000
                },
                advanced: {
                    month: 150,
                    year: 1400
                }
            },
            currencyRates: {
                CNY: 10, //для изначальной обработки, позже в код подставляется корректное актуальное значение
                KZT: 11,
                RUS: 1
            },
            selectedTariff: 'standard',
            selectedCurrency: 'CNY',
            selectedPeriod: 'month',
            total: 0,
            discount: 0,
            totalWithDiscount: 0
        };
    },
    methods: {
        getExchangeRates() {
            let getExchangeList = `https://www.cbr-xml-daily.ru/daily_json.js`;
            axios.get(getExchangeList)
                .then((response) => {
                    this.results = response.data;
                    console.log(this.results);
                    this.currencyRates.CNY = this.results.Valute.CNY.Value;
                    this.currencyRates.KZT = this.results.Valute.KZT.Value;
                    this.calculateTotal();
                })
                .catch((error) => {
                    console.log('Произошла ошибка при выполнении запроса:', error);
                });
        },
        calculateTotal() {
            const rate = this.currencyRates[this.selectedCurrency];
            const price = this.tariffs[this.selectedTariff][this.selectedPeriod];
            this.total = price * rate;

            if (this.selectedPeriod === 'year') {
                // 1000 * 1
                const yearlyPrice = this.tariffs[this.selectedTariff]['year'] * rate;
                // 100 * 1
                const monthlyPrice = this.tariffs[this.selectedTariff]['month'] * rate;
                this.discount = monthlyPrice * 12 - yearlyPrice;
                this.totalWithDiscount = this.discount.toFixed(3);
                if (this.totalWithDiscount % 1 === 0) {
                    this.totalWithDiscount = parseInt(this.totalWithDiscount);
                }
            } else {
                this.discount = 0;
                this.totalWithDiscount = 0;
            }
        }
    },
    mounted() {
        this.calculateTotal();
    },
    created() {
        this.getExchangeRates();
    }
}
</script>

<style scoped>
.calculator-section {
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    background-color: #f5f5f5;
}

.calculator-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    max-width: 1200px;
    padding: 20px;
    background-color: #fff;
    border-radius: 10px;
    box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
}

.calculator-title {
    margin-bottom: 20px;
    color: #333;
    text-align: center;
}

label,
select,
p {
    width: 100%;
    margin-bottom: 10px;
}

select {
    padding: 10px;
    border-radius: 5px;
    border: 1px solid #ccc;
}

p {
    color: #333;
}

@media (max-width: 768px) {
    .calculator-container {
        width: 90%;
    }
}
</style>